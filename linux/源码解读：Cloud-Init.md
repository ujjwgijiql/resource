# 源码解读：Cloud-Init

cloud-init 是 linux 的一个工具，当系统启动时，cloud-init 可从 nova metadata 服务或者 config drive 中获取 metadata，完成一些虚拟机的初始化工作，这些工作有可能是每台虚拟机例行的动作，如配置ip，也有可能是定制化动作，如注入密码等。

为了实现 instance 定制工作，cloud-init 会按 4 个阶段执行任务：

1. local
2. init
3. config
4. final

## 1 如何保证启动顺序？

这些任务都是以服务存在，并且安装完成后自动加入了开机自启。

![CentOS 7.x](http://image.iswbm.com/20190430203920.png)

CentOS 7.x

那么你一定会奇怪，这些任务都是分离的，如何控制其启动顺序呢？

在**CentOS 7.x** 中，所有的服务都是通过systemd 管理的，服务的启动依赖也可以在 Service 中声明，会开机时 systemd 做为第一个服务启动，会将所有的依赖关系都理出来，按顺序去启动。

比如 local 阶段作为第一个执行的。

![image1](http://image.iswbm.com/20190430204707.png)

而后的几个服务（如init阶段），都需要保证 后于 local 已经启动过才能运行。

![image2](http://image.iswbm.com/20190430204933.png)

在 **CentOS 6.x** 中，由于系统过于古老并没有 systemd ，它的服务启动顺序是由 `/etc/rc.d/rc3.d` 目录进行管理的，按照编号进行从小到大执行。

![image3](http://image.iswbm.com/20190430205449.png)

这个启动顺序相当重要，在排查并修复虚拟机创建时 ip 的配置问题的时候会有帮助，主要是 CentOS6.x 上的问题（我曾经碰到过的），这种也顺便讲讲。

假如我们要创建 一个CentOS 6.5 的虚拟机，而我们希望这个虚拟机的 ip 是静态ip（写在配置文件里），当你在虚拟机里使用的是NetworkManager（而非 network）时，会发现 ip 并不会自动配置上。而如果创建的是DHCP ip 的虚拟机时，就能正常配置ip。这是个很诡异的事情，曾经我也排查了许久。

在解释这个问题之前，先要理解 cloudinit 在local 阶段做了啥事。通过阅读源代码可知，在local阶段它会从 datasource 里读取网络配置信息，如果发现使用的是静态ip，cloudinit就会将网络信息（ip，dns，gateway等） 写入ifcfg配置文件。而如果发现使用的是 DHCP，cloudinit 并不会创建刷新网卡配置文件，配置ip的工作就交由 NetworkManager 会去自动获取。

从以信息可知，如果创建静态ip的虚拟机，NetworkManager 这个服务必须在 cloudinit-local 之后启动才可正常从配置文件中读取 ip 并配置。而当你在镜像里安装 NetworkManager后，默认情况下它的启动顺序是会在 cloudinit-local 之前的。

![image4](http://image.iswbm.com/20190430211900.png)

解决方法也很简单，将 `S23NetworkManager` 重命名为 `S54NetworkManager` 即可。

## 8.6.2 如何顺利地调试？

好了。回归 cloudinit 这几个阶段，这几个阶段正常情况下，只在虚拟机启动的时候才会执行。当你需要开发调试，你可以通过执行命令来手动执行。

1. cloud-init init –local 或者 cloud-init init -l
2. cloud-init init 或者 cloud-init modules -m init
3. cloud-init modules -m config
4. cloud-init modules -m final

除了这几个命令之外，还有几个：

- cloud-init query -n [name]
- cloud-init single -n [name] –frequency

以上，这些命令都是看源码我才得知的，以下是对应阶段处理的函数

- `init`：main_init
- `modules`：main_modules
- `query`：main_query
- `single`：main_single

这里再顺便说一点，在使用 pdb 进行调试时，你会发现一旦执行就会报错或者无法调试。

![image5](http://image.iswbm.com/20190430213012.png)

这里就需要一个小技巧。你只需修改cloudinit 的入口文件（CentOS6.x 是在/usr/bin/cloud-init，CentOS7.x 是在 /usr/lib/python2.7/site-packages/cloudinit/cmd/main.py）将红框这行注释。

![image6](http://image.iswbm.com/20190430213337.png)

再尝试即可正常调试。

![image7](http://image.iswbm.com/20190430213429.png)

从这里也学到了一点，如何要关闭调试功能，只要关闭标准输入就行了。

![image8](http://image.iswbm.com/20190430213729.png)

如果你在调试 single，而且你把 /var/lib/cloud 目录删除过，请务必要执行一下 cloud-init init ,不然会取不到 user_data

centos 6.5 的cloudinit 使用的是 python2.6，没有 format 语法，请注意不要使用。这是和 centos7的一点区别。

## 8.6.3 数据源是如何读取的？

数据源的读取入口是在 入口文件(CentOS6.x 是在 `/usr/bin/cloud-init`，CentOS7.x 是在 `/usr/lib/python2.7/site-packages/cloudinit/cmd/main.py`)的 main_init 函数中，会调用 `stages.py:fetch()` 函数。

![image9](http://image.iswbm.com/20190430225605.png)

支持哪些源：是在代码里（settings.py）定义写死的

![image10](http://image.iswbm.com/20190430225726.png)

如果你的虚拟机固定只使用其中一种，那可以只留一种，加快cloudinit 的执行速度。

find_source 是通过一个一个去执行 对应source模块的 get_data()，如果获取到了数据就直接返回。

![image11](http://image.iswbm.com/20190430230214.png)

最经常使用的是 ConfigDrive，来看一下它是如何获取数据的。

![image12](http://image.iswbm.com/FpqcyL4hWwpaAGzsdreQwXvH4Rx8)

它会先创建一个临时目录，尝试将 `/dev/sr0` 挂载到这个目录下。

![image13](http://image.iswbm.com/20190430230839.png)

然后将读取的数据存入 `/var/lib/cloud/instance/obj.pkl`， 而后续执行都将从这里反序列化，提高速度。

![image14](http://image.iswbm.com/20190430231108.png)

## 8.6.5 userdata 使用说明

现在 Userdate 可以支持如下三种格式

- User-Data Script
- Cloud Config Data
- Upstart Job

### User-Data Script

**作用**：写入shell脚本内容，在虚拟机创建的时候执行脚本

**配置文件**：myscript.sh

content-types：text/x-shellscript

```
#!/bin/sh
echo "Hello World.  The time is now $(date -R)!" | tee /root/output.txt
```

### Cloud Config Data

**作用**：注入用户数据。

配置项格式注意是yaml 格式。

1. 可以用write_files，将用户的数据写入指定路径文件中，并设置权限，指定编码等功能；
2. 可以用ws_virt_network_dep，指定虚IP创建虚拟机。
3. 可以用runcmd，在虚拟机创建的时候执行，执行的输出结果将记录在/var/log/cloud-init-output.log
4. 可以用 resolv_conf，（仅RHEL系列系统可用）指定创建虚拟机后配置的 dns，注意要同时配置上manage-resolv-conf: true
5. 可以用 ssh_authorized_keys，将指定的公钥注入虚拟机

**配置文件**：myconfig.cfg

content-types：text/cloud-config

```
#cloud-config
write_files:
-   encoding: b64
    content: CiMgVGhpcyBmaWxlIGNvbnRyb2xzIHRoZSBzdGF0ZSBvZiBTRUxpbnV4
    owner: root:root
    path: /etc/sysconfig/selinux
    permissions: '0644'
-   content: |
        # My new /etc/sysconfig/samba file
        SMBDOPTIONS="-D"
    path: /etc/sysconfig/samba
-   content: !!binary |
        H4sIAIDb/U8C/1NW1E/KzNMvzuBKTc7IV8hIzcnJVyjPL8pJ4QIA6N+MVxsAAAA=
    path: /bin/arch
    permissions: '0555'
-   encoding: gzip
    content: !!binary |
        H4sIAIDb/U8C/1NW1E/KzNMvzuBKTc7IV8hIzcnJVyjPL8pJ4QIA6N+MVxsAAAA=
    path: /usr/bin/hello
permissions: '0755'

ws_virt_network_dep:
    36.250.74.xx:
        port_0:
            ip: 192.168.2.10
            netmask: 255.255.255.0
    10.10.10.xx:
        port_0:
            ip: 192.168.3.10
            netmask: 255.255.255.0
            gateway: 192.168.3.254

# 会将这些命令写入如下文件，但是并没有执行
# /var/lib/cloud/instances/25be4404-4665-4b64-91cc-3289cf2a0af0/scripts/runcmd
runcmd:
  - [ sed, -i, -e, '%s/x/y/g', some_file]
  - echo "modified some_file"
  - [cat, some_file]

manage-resolv-conf: true
resolv_conf:
  nameservers:
    - '8.8.8.8'
    - '114.114.114.114'

ssh_authorized_keys:
  - ssh_key_1
  - ssh_key_2
```

### Upstart Job

用处：设置开机启动项，将文件放入 /etc/init/ 目录下，生成的文件，名为 [filename].conf，对于这个例子，文件名即为myjob.cfg.conf

**配置文件**：myjob.cfg

content-types：text/upstart-job

```
#upstart-job
#!/bin/sh
echo "Hello World. The time is now $(date -R)!" | tee /root/output.txt
```

### 混合模式

配置好所需配置文件内容后。这里一个辅助脚本来将三种格式的配置合并。

**辅助脚本**：generate_mime_messages.py

```
#!/usr/bin/python

import sys

from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

if len(sys.argv) == 1:
    print("%s input-file:type ..." % (sys.argv[0]))
    sys.exit(1)

combined_message = MIMEMultipart()
for i in sys.argv[1:]:
    (filename, format_type) = i.split(":", 1)
    with open(filename) as fh:
        contents = fh.read()
    sub_message = MIMEText(contents, format_type, sys.getdefaultencoding())
    sub_message.add_header('Content-Disposition', 'attachment; filename="%s"' % (filename))
    combined_message.attach(sub_message)

print(combined_message)
```

执行如下命令

```
python generate_mime_messages.py \
myscript.sh:text/x-shellscript \
myconfig.cfg:text/cloud-config \
myjob.cfg:text/upstart-job
```

命令说明：

- myscript.sh、myconfig.cfg、myjob.cfg 是配置文件
- text/x-shellscript、text/cloud-config、text/upstart-job 是对应的内容content-types

执行完后就会生成一段内容，再将这段内容进行 base64 编码处理，就是userdata 的参数。

## 8.6.6 网卡名被重命名

当你在虚拟机上卸载网卡后，在没有指定原来网卡的mac地址的时候下，创建新的port再挂给虚拟机使用，如果虚拟机不经过重启的话，是不会有任何问题的。

但是，如果虚拟机重启了，你可能会发现没有了原来的eth0或者eth1，而多了一个 cirename0 的网卡。

这就是cloudinit搞的鬼，在cloudinit的local阶段，好像会记录之前的mac地址，如果发现不一致，就会触发rename_interface。

![image15](http://image.iswbm.com/20190623091911.png)

## 8.6.7 虚拟机启动卡住

当创建一个有数据盘的虚拟机，nova会在configdrive里的ec2目录下生成 meta-data.json ，其中有一个字段是block-device-mapping，包含磁盘信息。在虚拟机创建后，如果 /etc/cloud/cloud.cfg里配置了 mounts，cloudinit会根据这个这下面文件中的 ephemeral0 拿到对应的 /dev/vdb，并将其写入 /etc/fstab 中。在下次重启时，会根据 fstab 挂载磁盘，如果挂载不上，就会导致虚拟机启动卡住。

![image16](http://image.iswbm.com/20190708175813.png)

## 8.6.8 模块是怎么执行的

核心代码在 `_run_modules` 函数里：

```
# stages.py
def _run_modules(self, mostly_mods):
    cc = self.init.cloudify()
    # Return which ones ran
    # and which ones failed + the exception of why it failed
    failures = []
    which_ran = []
    for (mod, name, freq, args) in mostly_mods:
        try:
            # Try the modules frequency, otherwise fallback to a known one
            if not freq:
                freq = mod.frequency
            if freq not in FREQUENCIES:
                freq = PER_INSTANCE
            LOG.debug("Running module %s (%s) with frequency %s",
                      name, mod, freq)

            # Use the configs logger and not our own
            # TODO(harlowja): possibly check the module
            # for having a LOG attr and just give it back
            # its own logger?
            func_args = [name, self.cfg,
                         cc, config.LOG, args]
            # Mark it as having started running
            which_ran.append(name)
            # This name will affect the semaphore name created
            run_name = "config-%s" % (name)

            desc = "running %s with frequency %s" % (run_name, freq)
            myrep = events.ReportEventStack(
                name=run_name, description=desc, parent=self.reporter)

            with myrep:
                # 执行模块
                ran, _r = cc.run(run_name, mod.handle, func_args,
                                 freq=freq)
                if ran:
                    myrep.message = "%s ran successfully" % run_name
                else:
                    myrep.message = "%s previously ran" % run_name
```

上面的 cc.run()，代码如下：

```
# helpers.py
def run(self, name, functor, args, freq=None, clear_on_fail=False):

    # 获取 sem 文件。
    sem = self._get_sem(freq)
    if not sem:
        sem = DummySemaphores()
    if not args:
        args = []

    # 判断是否已经执行过（准确说是，是否需要执行），具体逻辑可以参考上面 8.6.8 章节的逻辑。
    if sem.has_run(name, freq):
        LOG.debug("%s already ran (freq=%s)", name, freq)
        return (False, None)

    # 如果需要执行，就在对应的 sem 目录下生成一个文件（锁）
    with sem.lock(name, freq, clear_on_fail) as lk:
        if not lk:
            raise LockFailure("Failed to acquire lock for %s" % name)
        else:
            LOG.debug("Running %s using lock (%s)", name, lk)
            if isinstance(args, (dict)):
                # 然后去执行这个模块
                results = functor(**args)
            else:
                results = functor(*args)
            return (True, results)
```

## 8.6.9 模块执行的频率

在 cloudinit 里的各个模块里，都可以配置执行频率。

- PER_ALWAYS：总是执行
- PER_INSTANCE：每个虚拟机实例一次
- PER_ONCE：每个镜像只执行一次

对于 `PER_INSTANCE` 和 `PER_ONCE` 和区别，从名字和代码实现上看，似乎没有区别，继续看 cloudinit 里的代码，只有一个模块使用了 PER_ONCE ，那就是 `cc_scripts_per_once.py`。

![image17](http://image.iswbm.com/20190910160035.png)

其实他们还是有区别的，由下面的代码来看

- `PER_ONCE` 获取的 sem 文件是从 `/var/lib/cloud/` 下的文件获取的，这个目录一个镜像只会生成一次（若你不删除的话）。
- `PER_INSTANCE` 获取 sem 文件是从 `/var/lib/cloud/instances/<uuid>` 下的文件获取的，这个目录每个虚拟机一个。

![image18](http://image.iswbm.com/20190910150305.png)

如果你的模块里已经配置了频率，则以此为准。若没有配置，则默认为 `PER_INSTANCE`。

具体函数：`_run_modules`

![image19](http://image.iswbm.com/20190910142222.png)

那么cloudinit 是如何控制模块的执行频率呢？

通过代码可以发现，其在运行时，会先调用 `has_run` 函数，在这里会去取对应目录下（上面的 sem_path）的sem文件，如果有存在 sem 文件，说明已经执行过（返回 False ），如果不存在 sem 文件（返回 True） 说明未执行过。

![image20](http://image.iswbm.com/20190910153637.png)

sem 文件是怎样的？

这是 `PER_ONCE` 的 sem 文件：

![image21](http://image.iswbm.com/20190910171359.png)

这是 `PER_INSTANCE` 的 sem 文件![image22](http://image.iswbm.com/20190910171538.png)

## 8.6.10 如何解析网卡配置

ubuntu 的网卡配置不是正常我们常见的 json 或者 yaml 格式，若要将其转化成python的字典对象。这在cloudinit是怎么做的呢？

将这个功能拿出来 ，你可以这样用。

```
from cloudinit.net import eni
config=eni.parse_deb_config('/etc/network/interfaces.d/50-cloud-init.cfg')
```

![image23](http://image.iswbm.com/20190906091102.png)

## 8.6.11 低版本的日志输出

如果你用过 centos6.x 的 cloudinit，你会发现其根本不会将日志输出到 `/var/log/cloud-init.log`中。

经过排查，你可以在 `cloudinit/log.py` 中按下面的改法修改解决

![image24](http://image.iswbm.com/20190909172153.png)

## 8.6.12 旧版本网络配置的三个巨坑

在不同的 cloudinit 版本中，网络配置部分的代码是不同的。

首先要知道，配置网络是在 `on_first_boot` 函数里配置的。它是在cloudinit 判断该机器为新虚拟机时才会执行。也就是说，这个网络配置在一个虚拟机生命周期里，只会执行一次，如果要二次执行，需要执行 `rm -rf /var/lib/cloud` ，将缓存数据删除，这边才会重新认定为新虚拟机。

这里仅以 cloudinit 0.7.5 （ CentOS6.x）的版本为例。

在 cloud-init 0.7.5中，网络信息的读取与配置都是在且仅能在 local 阶段进行的，代码如下，只在 dsmode 为 local 时才会执行 on_first_boot。

![image25](http://image.iswbm.com/20190429104357.png)

而如果是虚拟机重启的话，是在stages.py 这边判断是否为新虚拟机的，这也和旧版本有所区别。

![image26](http://image.iswbm.com/20190829141059.png)

为了让你能更加清晰的了解这个网络配置过程，我阅读了这块的源代码。

在 cloud-init 的比较重要的几个文件有：

- 入口文件（上面已经说明过了）
- stages.py
- distros/rhel.py
- sources/DataSourceConfigDrive.py

在网络配置这块，有几个大坑。

**坑一**

如果是按照旧虚拟机创建新的快照镜像，然后使用这个镜像创建新的虚拟机，有可能会在同一块网卡上出现新旧两个ip，这是因为虚拟机在启动过程中，会先读取原网络配置配置ip，然后才会运行 cloud-init 进行新ip的配置，而新ip的配置是使用 `ifup` 这个命令![image27](http://image.iswbm.com/Fp1TeHSiIMIQoZygbW9VSfAagB_d)

使用这种方式并不会将第一次配置的旧ip给清除掉。

![image28](http://image.iswbm.com/Fh-5SQ8qYjhJEKovI6LmIpabSy2c)

这个问题，目前我只在CentOS6 中遇到过。可以通过修改代码让其先 `ifdown` 再 `ifup` 就可以解决这个问题。![image29](http://image.iswbm.com/20190430231812.png)

**坑二**

如果使用dhcp，cloudinit不会创建或者刷新网卡配置文件，而把配置ip交由 NetworkManager ，让它自动获取。这是很重要。所以如果你用旧虚拟机创建快照的方式做镜像，然后用这个镜像创建的虚拟机会有旧虚拟机的配置文件会暴露原机器的ip地址。

具体的创建逻辑是在这

![image30](http://image.iswbm.com/20190430232309.png)

**坑三**

在 CentOS 6 上，安装NetworkManager 时不会安装完整。

会导致两个问题，一个是在启动时，会提示无法加载插件，导致启动失败。

```
Apr 29 11:13:29 localhost NetworkManager[1365]: <info> Loaded plugin keyfile: (cc
) 2007 - 2008 Red Hat, Inc.  To report bugs please use the NetworkManager mailinn
g list.
Apr 29 11:13:29 localhost NetworkManager[1365]: <error> [1556507609.466522] [maii
n.c:708] main(): failed to initialize the network manager: Could not load pluginn
 'ibft': /usr/lib64/NetworkManager/libnm-settings-plugin-ibft.so: undefined symbb
ol: g_slist_free_full
Apr 29 11:13:29 localhost NetworkManager[1365]: <info> exiting (error)
```

一个是会自动DHCP获取到一个以ip命名的hostname，并将原来的覆盖掉。

![image31](http://image.iswbm.com/20190429205735.png)

为了避免出现这些情况，请务必保证这些包都安装完整（左为 CentOS 7.2，右为 CentOS 6.5）。

![image32](http://image.iswbm.com/20190430232911.png)

## 8.6.13 网络是如何启动的？（新版本）

这里仅以 cloudinit 18.5 的版本为例。

通过查看代码主流程时，获取ds的时候，有一个参数是 existing，它有两个值：

- trust：说明 ds 的缓存是可以相信的，不用再去校验 instance_uuid
- check：说明会去校验 instance_uuid，如果相同直接返回 ds 的缓存，如果不同则返回None，让后的步骤再去从 CD-ROM 读取最新的。

在 local 阶段的时候，`existing` 是 `check` ，这是合理的。

![image33](http://image.iswbm.com/20190911175423.png)

![image34](http://image.iswbm.com/20190911174648.png)

在local阶段，on_first_boot 的函数 network 是 False ，所以这里也不会写网卡配置文件，自然也不会配置。

![image35](http://image.iswbm.com/20190911173615.png)

![image36](http://image.iswbm.com/20190911195024.png)

再往后面看，就可以发现，原来写配置文件的地方是在 `cmd/main.py` 里。

```
# cmd/main.py
mode = sources.DSMODE_LOCAL if args.local else sources.DSMODE_NETWORK

# 中间省略多行代码 ...
init.apply_network_config(bring_up=bool(mode != sources.DSMODE_LOCAL))
```

第一行，如果是 local 阶段，mode 为 local，bring_up 为 False，意思是只写配置文件，而不启用网卡。

等到 init 阶段时，mode 为 net （`sources.DSMODE_NETWORK`）时，bring_up 为 True，意思是会启用网卡，配置ip。

网卡网卡的主要函数在下面 `apply_network_config` 这个函数里，这个函数主要做三个事情：

1. 获取网卡配置
2. 校正网卡名，以 ConfigDrive 的配置为准
3. 将ip信息写入配置文件，并启动网卡

![image37](http://image.iswbm.com/20190911202425.png)

那它是如何对网卡进行重命令的呢？看了代码，其实是用ip命令实现的。

![image38](http://image.iswbm.com/20190911202551.png)

提取出来其实就三条命令，要注意的是，这三条命令执行是有顺序的

```
1. ip link set ens3 down
2. ip link set ens3 name eth0
3. ip link set eth0 up
```

还有一点要说的是，cloudinit 是如何取到本机真实的网卡信息的呢？他是从 `/sys/class/net/` 目录下获取的。每个网卡一个目录，每个目录下都有相应的文件记录相应的信息，比如 `/sys/class/net/ens3/address` 记录的是网卡的 mac 地址。

![image39](http://image.iswbm.com/20190911203953.png)

接下来就要开始配置网络了，先写网络配置文件，再根据参数选择是否启用网络。

![image40](http://image.iswbm.com/20190911204805.png)

如果是重启虚拟机或者 init 阶段进入这里呢，会不会又重复配置网络了呢？

答案是：不会的。

cloudinit 会根据缓存中的虚拟机的uuid来与ConfigDrive 的对比，如果不一样，则认为这台虚拟机是新创建的虚拟机，只有新的虚拟机才会走入这里去配置网络。

![image41](http://image.iswbm.com/20190911205518.png)

那问题又来了，虽然上面有个 bring_up 的参数，实际上，通过代码可以发现，在 local 阶段，bring_up 为 False 不会去启用网卡，而在 init 阶段呢，虽然 bring_up 为 True，但是此时，经过 local 阶段后，代码逻辑会认为这是台旧虚拟机，不会再走后面配置网络的函数，那就很奇怪了，网卡的ip是如何配置上的呢？

这是个好问题，也是个很难察觉的点。

大多数人，可能不知道linux内部的服务是有启动顺序的。在这种情况下，我们必须保证，cloudinit 写入配置的服务（cloud-init-local）必须在 network 或者 Network-Manager 的服务之前。

这样在 cloud-init-local 执行完后，就会自动配置上网络了。

## 8.6.14 在虚拟机启动时执行命令

cloudinit 允许通过 user_data 指定你想在虚拟机启动时，执行的命令。

```
# 会将这些命令写入如下文件，但是并没有执行
#
runcmd:
  - [ sed, -i, -e, '%s/x/y/g', some_file]
  - echo "modified some_file"
  - [cat, some_file]
```

当你进行了如此配置后，cloudinit 会通过 runcmd 模块，将这些命令组合起来写入 `/var/lib/cloud/instances/<uuid>/scripts/runcmd`。

但这仅仅只是写入，若要执行这些命令，还需要你在 /etc/cloud/cloud.cfg 中配置 `scripts_user`，这样 cloud-init 才会去执行它。

## 8.6.5 如何读取修改缓存数据

当虚拟机启动时，cloudinit 会将 configdrive 里的数据读取并缓存，缓存文件是一个被序列化的二进制文件，名字固定为 `obj.pkl`。

序列化的函数存在于 stages.py 文件

- `_pkl_store()`：序列化
- `_pkl_load()`：反序列化

![image42](http://image.iswbm.com/20200807135831.png)

读取演示

```
>>> from cloudinit import stages;
>>> file=stages._pkl_load("/var/lib/cloud/instances/1242171a-bf72-4a3a-acb0-9e5393e97865/obj.pkl")
>>> file.network_json
>>> file.metadata
```

写入演示

```
>>> file.metadata["vhost_queues"]=1
>>> stages._pkl_store(file, "/home/obj.pkl")
True
```

## 8.6.16 如何判断是新虚拟机

在 stages.py 下有一个函数 `is_new_instance()`

![image43](http://image.iswbm.com/20200807161244.png)

其中 `previous` 和 `nw_timestramp` 都是从 `/var/lib/cloud/instances/[instance_uud]/` 目录下直接读取的，导致旧虚拟机的信息。

而在 `/var/lib/cloud/instance` 目录下的信息，都是从 `/dev/sr0` 从读取的最新信息。

通过二者对比，就可以知道该虚拟机是否是一台新的虚拟机。

## 8.6.17 CentOS 6.x 如何更改网卡名

参考文章：[Changing the ethX to Ethernet Device Mapping in EL6](https://www.alteeve.com/w/Changing_the_ethX_to_Ethernet_Device_Mapping_in_EL6)

关闭network

```
$ /etc/init.d/network stop
```

修改ifcfg 文件里的 device 和 hwaddr

```
$ vim /etc/sysconfig/network-scripts/ifcfg-eth*
```

修改 udev 网卡规则文件

```
vim /etc/udev/rules.d/70-persistent-net.rules
```

重新加载 udev，启动 network

```
$ start_udev && /etc/init.d/network start
```

## 8.6.18 相关命令

```
# 查看机器里有哪几张网卡
ls -l /sys/class/net

# 只查看 ipv4 或 ipv6的网卡ip
ip -6 addr show
ip -4 addr show

# 网络启动不来，先 flush 试一下
ip addr flush dev ens3
```