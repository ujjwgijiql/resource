# Linux中service命令和/etc/init.d/的关系

service xxx启动 /etc/init.d/ 目录下的xxx脚本
如一个脚本名为 mysvc保存在/etc/init.d/下可以用 service mysvc 运行mysvc脚本
echo passed in option $0 $1
 ```shell
if [ $1 = "start" ]
then
        echo myservice started........................................
elif [ $1 = "stop" ]
then
        echo myservice stoped........................................
else
        echo myservice not supported option........................................
fi
 ```    
 ```shell
[root@localhost init.d]# service mysvc start
passed in option /etc/init.d/mysvc start
myservice started........................................
 
[root@localhost init.d]# service mysvc stop
passed in option /etc/init.d/mysvc stop
myservice stoped........................................
 
[root@localhost init.d]# service mysvc xxx
passed in option /etc/init.d/mysvc xxx
myservice not supported option........................................
```

&nbsp;    
# CentOS如何查看端口是被哪个应用/进程占用
有时启动应用时会发现端口已经被占用，或者是感觉有些端口自己没有使用却发现是打开的。这时我们希望知道是哪个应用/进程在使用该端口。    

CentOS下可以用netstat或者lsof查看，Windows下也可以用netstat查看，不过参数会不同

Linux:
netstat -nap           # 会列出所有正在使用的端口及关联的进程/应用    
lsof -i :portnumber    # portnumber要用具体的端口号代替，可以直接列出该端口听使用进程/应用    

__一、检查端口被哪个进程占用__    
检查88端口被哪个进程占用    
```shell
netstat -lnp | grep 88
```    
__二、查看进程的详细信息__   
```shell
ps 1777
```    
__三、杀掉进程__    
```shell
kill -9 1777
```    
&nbsp;    
__选项说明：__    
```shell
-a或--all：显示所有连线中的Socket；
-A<网络类型>或--<网络类型>：列出该网络类型连线中的相关地址；
-c或--continuous：持续列出网络状态；
-C或--cache：显示路由器配置的快取信息；
-e或--extend：显示网络其他相关信息；
-F或--fib：显示FIB；
-g或--groups：显示多重广播功能群组组员名单；
-h或--help：在线帮助；
-i或--interfaces：显示网络界面信息表单；
-l或--listening：显示监控中的服务器的Socket；
-M或--masquerade：显示伪装的网络连线；
-n或--numeric：直接使用ip地址，而不通过域名服务器；
-N或--netlink或--symbolic：显示网络硬件外围设备的符号连接名称；
-o或--timers：显示计时器；
-p或--programs：显示正在使用Socket的程序识别码和程序名称；
-r或--route：显示Routing Table；
-s或--statistice：显示网络工作信息统计表；
-t或--tcp：显示TCP传输协议的连线状况；
-u或--udp：显示UDP传输协议的连线状况；
-v或--verbose：显示指令执行过程；
-V或--version：显示版本信息；
-w或--raw：显示RAW传输协议的连线状况；
-x或--unix：此参数的效果和指定"-A unix"参数相同；
--ip或--inet：此参数的效果和指定"-A inet"参数相同。
```   
