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
&nbsp;&nbsp;&nbsp;&nbsp;    
# pidstat 示例    
pidstat 的用法：    
```shell
pidstat [ 选项 ] [ <时间间隔> ] [ <次数> ]
```    
```shell
[********]#pidstat -help
Usage: pidstat [ options ] [ <interval> [ <count> ] ]
Options are:
[ -d ] [ -h ] [ -I ] [ -l ] [ -r ] [ -s ] [ -t ] [ -U [ <username> ] ] [ -u ]
[ -V ] [ -w ] [ -C <command> ] [ -p { <pid> [,...] | SELF | ALL } ]
[ -T { TASK | CHILD | ALL } ]
```
常用的参数：

* -u：默认的参数，显示各个进程的cpu使用统计    
* -r：显示各个进程的内存使用统计    
* -d：显示各个进程的IO使用情况
* -p：指定进程号
* -w：显示每个进程的上下文切换情况
* -t：显示选择任务的线程的统计信息外的额外信息
* -T { TASK | CHILD | ALL }
这个选项指定了pidstat监控的。TASK表示报告独立的task，CHILD关键字表示报告进程下所有线程统计信息。ALL表示报告独立的task和task下面的所有线程。
注意：task和子线程的全局的统计信息和pidstat选项无关。这些统计信息不会对应到当前的统计间隔，这些统计信息只有在子线程kill或者完成的时候才会被收集。
* -V：版本号
* -h：在一行上显示了所有活动，这样其他程序可以容易解析。
* -I：在SMP环境，表示任务的CPU使用率/内核数量
* -l：显示命令名和所有参数    
&nbsp;&nbsp;&nbsp;&nbsp;    
# 示例一：查看所有进程的 CPU 使用情况（ -u -p ALL）    
```shell
pidstat
pidstat -u -p ALL
```
pidstat 和 pidstat -u -p ALL 是等效的。    
pidstat 默认显示了所有进程的cpu使用率。    
```shell
[root@********]# pidstat
Linux 3.10.0-693.el7.x86_64 (arch-dev07)        12/21/2018      _x86_64_        (2 CPU)

01:18:34 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command
01:18:34 PM     0         1    0.00    0.01    0.00    0.01     1  systemd
01:18:34 PM     0         9    0.00    0.00    0.00    0.00     0  rcu_sched
01:18:34 PM     0        10    0.00    0.00    0.00    0.00     0  watchdog/0
01:18:34 PM     0        11    0.00    0.00    0.00    0.00     1  watchdog/1
01:18:34 PM     0        13    0.00    0.00    0.00    0.00     1  ksoftirqd/1
01:18:34 PM     0        14    0.00    0.01    0.00    0.01     1  kworker/1:0
01:18:34 PM     0        26    0.00    0.00    0.00    0.00     1  kworker/1:1
01:18:34 PM     0        30    0.00    0.00    0.00    0.00     0  khugepaged
01:18:34 PM     0        40    0.00    0.00    0.00    0.00     0  kworker/u4:1
01:18:34 PM     0       261    0.00    0.00    0.00    0.00     0  kworker/0:2
01:18:34 PM     0       272    0.00    0.00    0.00    0.00     0  jbd2/sda2-8
01:18:34 PM     0       329    0.00    0.00    0.00    0.00     0  kworker/0:1H
01:18:34 PM     0       343    0.00    0.00    0.00    0.00     0  systemd-journal
01:18:34 PM     0       356    0.00    0.00    0.00    0.00     0  systemd-udevd
01:18:34 PM     0       446    0.00    0.00    0.00    0.00     1  auditd
01:18:34 PM   998       471    0.00    0.00    0.00    0.00     1  lsmd
01:18:34 PM     0       472    0.00    0.00    0.00    0.00     1  systemd-logind
01:18:34 PM   999       474    0.00    0.00    0.00    0.00     0  polkitd
01:18:34 PM     0       475    0.00    0.00    0.00    0.00     0  abrtd
01:18:34 PM     0       476    0.00    0.00    0.00    0.00     0  abrt-watch-log
01:18:34 PM     0       483    0.00    0.00    0.00    0.00     0  irqbalance
01:18:34 PM     0       485    0.00    0.00    0.00    0.00     1  smartd
01:18:34 PM     0       488    0.00    0.00    0.00    0.00     0  rsyslogd
01:18:34 PM    81       489    0.00    0.00    0.00    0.00     0  dbus-daemon
01:18:34 PM     0       497    0.00    0.00    0.00    0.00     0  NetworkManager
01:18:34 PM     0       499    0.00    0.00    0.00    0.00     0  crond
01:18:34 PM     0       724    0.01    0.00    0.00    0.01     0  tuned
01:18:34 PM     0       918    0.00    0.00    0.00    0.00     0  master
01:18:34 PM     0       991    0.00    0.00    0.00    0.00     1  kworker/1:1H
01:18:34 PM     0      1092    0.01    0.01    0.00    0.02     1  sshd
01:18:34 PM     0      1094    0.00    0.00    0.00    0.00     0  bash
01:18:34 PM     0      1427    0.01    0.01    0.00    0.02     0  sshd
01:18:34 PM     0      1429    0.00    0.00    0.00    0.00     0  bash
01:18:34 PM     0      1467    0.02    0.00    0.00    0.02     1  sshd
01:18:34 PM     0      1469    0.00    0.00    0.00    0.00     0  sftp-server
01:18:34 PM     0      2386    0.87    0.19    0.00    1.06     1  java
```


