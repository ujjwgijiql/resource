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

一、检查端口被哪个进程占用    
检查88端口被哪个进程占用    
```shell
netstat -lnp | grep 88
```    
二、查看进程的详细信息   
```shell
ps 1777
```    
三、杀掉进程    
```shell
kill -9 1777
```    
