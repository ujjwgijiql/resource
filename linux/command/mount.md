# mount的艺术

在阅读本文之前，我假设你已经对Linux系统下的硬盘、光盘的设备命令规则有所了解，比如sda和sda1的关系，以及hda、sda、fd、cdrom等设备。

### 1、我把U盘插到USB口上了，下一步我该如何做才能查看U盘里的内容？
我不能确定你的Linux系统会不会自动加载。  
所以，你应该先去/media目录下查看一下，看看是不是已经自动加载上了。  
如果是的话，应该在/media里有一个目录，名字类似于disk，你进入目录就等于进入你的U盘了。  
如果/media没有你想要的东西，那就要自己mount了！  
```shell
# mkdir /mnt/usb
# mount -t vfat /dev/sda /mnt/usb
```
这样就可以了，你去/mnt/usb目录看看，应该有你想要的了。  
如果mount命令不管用，那么你可以将/dev/sda改成/dev/sdb或者/dev/sdc试试！  
-t ：用于设定文件系统类型，我假设你的U盘是fat32的。（因为大部分U盘都是这样的）  
如果是其他文件系统，你可以man mount来查看一下，方法类似。  
&nbsp;&nbsp;

### 2、我把光盘放到光驱里了，下一步咋办？
如果系统没有自动加载光驱的话，那么用下面命令一般有效：
```shell
# mkdir /mnt/cdrom
# mount -t iso9660 /dev/cdrom /mnt/cdrom  
```

### 3、我下载了一个pes6.iso文件，我怎么加载iso文件呢？
```shell
# mount -o loop pes6.iso /mnt/cdrom
```
使用-o选项和loop参数即可！
&nbsp;&nbsp;

### 4、我下载了10个iso文件，从fifa2000.iso到fifa2009.iso，但是当我mount到fifa2007.iso时，就提示不让我mount了！怎么解决？
这个涉及到mount -o loop的原理了。你查看一下/dev下的以loop开头的文件，你就会发现  
```shell
# ls /dev/loop*
/dev/loop0  /dev/loop2  /dev/loop4  /dev/loop6
/dev/loop1  /dev/loop3  /dev/loop5  /dev/loop7
```
这下知道了吧，原来在/dev里只预设了8个加载iso的地方。  
解决办法是这样的：  
可以先用modinfo命令查看loop信息：  
```shell
# modinfo loop
filename:       /lib/modules/2.6.26.5-45.fc9.i686/kernel/drivers/block/loop.ko
alias:          block-major-7-*
license:        GPL
srcversion:     7595F5D46DFDB4A2D489DDD
depends:
vermagic:       2.6.26.5-45.fc9.i686 SMP mod_unload 686 4KSTACKS
parm:           max_loop:Maximum number of loop devices (int)
parm:           max_part:Maximum number of partitions per loop device (int)
```
可以看出max_loop来限制最大loop的数目。而max_part用来设定每个loop的设备所能支持的分区数目。  
你还可以看到控制loop的是一个内核模块：
```shell
# /lib/modules/2.6.26.5-45.fc9.i686/kernel/drivers/block/loop.ko
```
我们的目标就是修改max_loop值。
```shell
# echo “options loop max_loop=20”>>/etc/modprobe.conf
```
然后就是要重新加载内核模块，这样来做
```shell
# modprobe -l|grep loop.ko
/lib/modules/2.6.26.5-45.fc9.i686/kernel/drivers/block/cryptoloop.ko
/lib/modules/2.6.26.5-45.fc9.i686/kernel/drivers/block/loop.ko
# modprobe -r loop
# modprobe loop
```
注意modprobe命令的选项-r表示remove，即删除某个模块。  
好了，就此我们重新加载了loop模块，你现在再用ls /dev/loop*看看，是不是已经有20个loop设备了。  
&nbsp;&nbsp;

### 5、我知道/etc/fstab文件里的内容是开机时加载的所有设备，那么mount能利用这个文件么？  
可以利用，你直接执行mount -a就可以了，mount会乖乖的按照/etc/fstab文件的内容来加载所有的设备。  
&nbsp;&nbsp;

### 6、我这里有不同的文件系统，我用mount加载时需要注意什么？
需要在mount时加-t来设定文件系统类别：  
如果是fat的，就要-t vfat；  
如果是linux的ext系统的，就要-t ext或-t ext2或-t ext3，未来会有-t ext4；  
如果是光驱，那就是-t iso9660；  
如果文件系统是ntfs，那么你可能需要了解一下ntfs-3g这个软件了。  
&nbsp;&nbsp;

### 7 我有一个老的三寸软盘，如果mount？
```shell
# mount -t vfat /dev/fd0 /mnt/floppy
```  

### 8 mount除了加载设备，还可以做什么？
mount可以用来装载支持acl的分区。具体可查询mount和acl。  
mount可以用来设定ftp登录目录，用–bind参数可以实现。  
mount可以设定和修改已加载的文件系统的属性，具体可查看mount的-o参数。  
