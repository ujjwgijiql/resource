# pidstat 概述    
pidstat是sysstat工具的一个命令，用于监控全部或指定进程的cpu、内存、线程、设备IO等系统资源的占用情况。
pidstat首次运行时显示自系统启动开始的各项统计信息，之后运行pidstat将显示自上次运行该命令以后的统计信息。
用户可以通过指定统计的次数和时间来获得所需的统计信息。    
&nbsp;&nbsp;&nbsp;&nbsp;    
# pidstat 安装    
从大多数linux发行版的软件源中获得。    

* 在Debian/Ubuntu系统中可以使用下面的命令来安装:    
```shell
apt-get install sysstat
```    
* CentOS/Fedora/RHEL版本的linux中则使用下面的命令：    
```shell
yum install sysstat
```    
