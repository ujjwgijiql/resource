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
