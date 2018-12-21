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
[root@********]#pidstat -help
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
Linux 3.10.0-693.el7.x86_64 (********)        12/21/2018      _x86_64_        (2 CPU)

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
__详细说明__
* PID：进程ID
* %usr：进程在用户空间占用cpu的百分比
* %system：进程在内核空间占用cpu的百分比
* %guest：进程在虚拟机占用cpu的百分比
* %CPU：进程占用cpu的百分比
* CPU：处理进程的cpu编号
* Command：当前进程对应的命令
&nbsp;&nbsp;&nbsp;&nbsp;    

# 示例二: cpu使用情况统计(-u)    
```shell
pidstat -u
```    
使用-u选项，pidstat将显示各活动进程的cpu使用统计，执行”pidstat -u”与单独执行”pidstat”的效果一样。    
&nbsp;&nbsp;&nbsp;&nbsp;    

# 示例三： 内存使用情况统计(-r)    
```shell
pidstat -r
```    
使用-r选项，pidstat将显示各活动进程的内存使用统计：    
```shell
[root@******** /]# pidstat -r
Linux 3.10.0-693.el7.x86_64 (********)        12/21/2018      _x86_64_        (2 CPU)

01:22:51 PM   UID       PID  minflt/s  majflt/s     VSZ    RSS   %MEM  Command
01:22:51 PM     0         1      0.65      0.00  125188   3608   0.09  systemd
01:22:51 PM     0       343      0.22      0.00   36828   2896   0.07  systemd-journal
01:22:51 PM     0       356      0.08      0.00   44544   2436   0.06  systemd-udevd
01:22:51 PM     0       364      0.03      0.00  118848   1264   0.03  lvmetad
01:22:51 PM     0       446      0.01      0.00   55452    876   0.02  auditd
01:22:51 PM   998       471      0.02      0.00    8532    772   0.02  lsmd
01:22:51 PM     0       472      0.07      0.00   24204   1608   0.04  systemd-logind
01:22:51 PM   999       474      0.14      0.00  536212  11980   0.31  polkitd
01:22:51 PM     0       475      0.10      0.00  219192   5380   0.14  abrtd
01:22:51 PM     0       476      0.12      0.00  216908   4668   0.12  abrt-watch-log
01:22:51 PM     0       483      1.23      0.00   21476   1184   0.03  irqbalance
01:22:51 PM     0       485      0.05      0.00  127888   2240   0.06  smartd
01:22:51 PM     0       488      0.15      0.00  216392   8184   0.21  rsyslogd
01:22:51 PM    81       489      0.05      0.00   26644   1844   0.05  dbus-daemon
01:22:51 PM     0       497      0.19      0.00  471720   8760   0.23  NetworkManager
01:22:51 PM     0       499      0.07      0.00  126228   1548   0.04  crond
01:22:51 PM     0       501      0.03      0.00   25856    916   0.02  atd
01:22:51 PM     0       503      0.02      0.00  110044    792   0.02  agetty
01:22:51 PM     0       723      0.08      0.00  105996   4040   0.10  sshd
01:22:51 PM     0       724      0.53      0.00  562408  16484   0.42  tuned
01:22:51 PM     0       918      0.03      0.00   91628   2184   0.06  master
01:22:51 PM    89       920      0.07      0.00   91800   3984   0.10  qmgr
01:22:51 PM     0      1092      0.11      0.00  150400   5328   0.14  sshd
01:22:51 PM     0      1094      0.22      0.00  116160   2844   0.07  bash
01:22:51 PM     0      1427      0.11      0.00  150400   5336   0.14  sshd
01:22:51 PM     0      1429      0.37      0.00  116160   2860   0.07  bash
01:22:51 PM     0      1467      0.11      0.00  150568   5404   0.14  sshd
01:22:51 PM     0      1469      0.09      0.00   51112   2116   0.05  sftp-server
01:22:51 PM    89      2323      0.07      0.00   91732   3960   0.10  pickup
01:22:51 PM     0      2386      8.83      0.00 5312640 1322168  34.06  java
01:22:51 PM     0      3111      0.03      0.00  108124    988   0.03  pidstat
```    
__详细说明__
* PID：进程标识符    
* Minflt/s:任务每秒发生的次要错误，不需要从磁盘中加载页    
* Majflt/s:任务每秒发生的主要错误，需要从磁盘中加载页    
* VSZ：虚拟地址大小，虚拟内存的使用KB    
* RSS：常驻集合大小，非交换区五里内存使用KB    
* Command：task命令名    
&nbsp;&nbsp;&nbsp;&nbsp;    

# 示例四：显示各个进程的IO使用情况（-d）    
```shell
pidstat -d
```    
```shell
[root@******** /]# pidstat -d
Linux 3.10.0-693.el7.x86_64 (********)        12/21/2018      _x86_64_        (2 CPU)

01:25:55 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
01:25:55 PM     0         1     20.28      1.69      0.09  systemd
01:25:55 PM     0         6      0.00      0.00      0.00  kworker/u4:0
01:25:55 PM     0        40      0.00      0.00      0.00  kworker/u4:1
01:25:55 PM     0       272      0.00      5.53      0.00  jbd2/sda2-8
01:25:55 PM     0       343      0.07      0.00      0.00  systemd-journal
01:25:55 PM     0       356      0.76      0.00      0.00  systemd-udevd
01:25:55 PM     0       364      0.00      0.00      0.00  lvmetad
01:25:55 PM     0       428      0.00      0.00      0.00  jbd2/sda1-8
01:25:55 PM     0       446      0.00      0.02      0.00  auditd
01:25:55 PM   998       471      0.03      0.00      0.00  lsmd
01:25:55 PM     0       472      0.03      0.00      0.00  systemd-logind
01:25:55 PM   999       474      0.36      0.00      0.00  polkitd
01:25:55 PM     0       475      0.05      0.00      0.00  abrtd
01:25:55 PM     0       476      0.17      0.00      0.00  abrt-watch-log
01:25:55 PM     0       483      0.01      0.00      0.00  irqbalance
01:25:55 PM     0       485      0.11      0.00      0.00  smartd
01:25:55 PM     0       488      0.06      0.05      0.00  rsyslogd
01:25:55 PM    81       489      0.06      0.00      0.00  dbus-daemon
01:25:55 PM     0       497      0.42      0.00      0.00  NetworkManager
01:25:55 PM     0       499      0.05      0.01      0.00  crond
01:25:55 PM     0       501      0.00      0.00      0.00  atd
01:25:55 PM     0       503      0.00      0.00      0.00  agetty
01:25:55 PM     0       723      0.23      3.46      0.01  sshd
01:25:55 PM     0       724      0.47      0.00      0.00  tuned
01:25:55 PM     0       918      0.06      0.00      0.00  master
01:25:55 PM    89       920      0.02      0.00      0.00  qmgr
01:25:55 PM     0      1092      0.03      0.00      0.00  sshd
01:25:55 PM     0      1094      2.01      0.01      0.00  bash
01:25:55 PM     0      1429      0.01     48.01      0.01  bash
01:25:55 PM     0      1469      0.00      6.90      0.00  sftp-server
01:25:55 PM     0      2386      0.00     13.38      0.00  java
```    
__详细说明__
* PID：进程id
* kB_rd/s：每秒从磁盘读取的KB
* kB_wr/s：每秒写入磁盘KB
* kB_ccwr/s：任务取消的写入磁盘的KB。当任务截断脏的pagecache的时候会发生。
* COMMAND:task的命令名    
&nbsp;&nbsp;&nbsp;&nbsp;    

# 示例五：显示每个进程的上下文切换情况（-w）
```shell
pidstat -w -p 2386
```
```shell
[root@******** /]# pidstat -w -p 2386 1
Linux 3.10.0-693.el7.x86_64 (********)        12/21/2018      _x86_64_        (2 CPU)

01:39:04 PM   UID       PID   cswch/s nvcswch/s  Command
01:39:05 PM     0      2386      0.00      0.00  java
01:39:06 PM     0      2386      0.00      0.00  java
01:39:07 PM     0      2386      0.00      0.00  java
01:39:08 PM     0      2386      0.00      0.00  java
```
__详细说明__
* PID:进程id
* Cswch/s:每秒主动任务上下文切换数量
* Nvcswch/s:每秒被动任务上下文切换数量
* Command:命令名    
&nbsp;&nbsp;&nbsp;&nbsp;    

# 示例六：显示选择任务的线程的统计信息外的额外信息 (-t)
```shell
pidstat -t -p 2386
```
```shell
[root@******** /]# pidstat -t -p 2386
Linux 3.10.0-693.el7.x86_64 (********)        12/21/2018      _x86_64_        (2 CPU)

01:41:27 PM   UID      TGID       TID    %usr %system  %guest    %CPU   CPU  Command
01:41:27 PM     0      2386         -    2.31    0.67    0.00    2.98     1  java
01:41:27 PM     0         -      2386    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2387    0.04    0.00    0.00    0.04     1  |__java
01:41:27 PM     0         -      2388    0.00    0.00    0.00    0.00     0  |__java
01:41:27 PM     0         -      2389    0.00    0.00    0.00    0.00     0  |__java
01:41:27 PM     0         -      2390    0.01    0.00    0.00    0.01     0  |__java
01:41:27 PM     0         -      2391    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2392    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2393    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2394    0.29    0.00    0.00    0.29     1  |__java
01:41:27 PM     0         -      2395    0.04    0.00    0.00    0.04     0  |__java
01:41:27 PM     0         -      2396    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2397    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2418    0.00    0.00    0.00    0.00     1  |__java
01:41:27 PM     0         -      2419    0.00    0.00    0.00    0.00     0  |__java
01:41:27 PM     0         -      2420    0.06    0.04    0.00    0.11     0  |__java
01:41:27 PM     0         -      2421    0.09    0.06    0.00    0.16     0  |__java
```
__详细说明__
* TGID:主线程的表示
* TID:线程id
* %usr：进程在用户空间占用cpu的百分比
* %system：进程在内核空间占用cpu的百分比
* %guest：进程在虚拟机占用cpu的百分比
* %CPU：进程占用cpu的百分比
* CPU：处理进程的cpu编号
* Command：当前进程对应的命令    
&nbsp;&nbsp;&nbsp;&nbsp;    

# 示例七：pidstat -T
```shell
pidstat -T TASK
pidstat -T CHILD
pidstat -T ALL
```
* TASK表示报告独立的task。
* CHILD关键字表示报告进程下所有线程统计信息。
* ALL表示报告独立的task和task下面的所有线程。

注意：task和子线程的全局的统计信息和pidstat选项无关。这些统计信息不会对应到当前的统计间隔，这些统计信息只有在子线程kill或者完成的时候才会被收集。
```shell
pidstat -T ALL -p 2386 10
```
```shell
[root@******** /]# pidstat -T ALL -p 2386 10
Linux 3.10.0-693.el7.x86_64 (********)        12/21/2018      _x86_64_        (2 CPU)

01:51:36 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command
01:51:46 PM     0      2386    2.50    0.60    0.00    3.10     1  java

01:51:36 PM   UID       PID    usr-ms system-ms  guest-ms  Command
01:51:46 PM     0      2386       250        60         0  java
```
* PID:进程id
* Usr-ms:任务和子线程在用户级别使用的毫秒数。
* System-ms:任务和子线程在系统级别使用的毫秒数。
* Guest-ms:任务和子线程在虚拟机(running a virtual processor)使用的毫秒数。
* Command:命令名

