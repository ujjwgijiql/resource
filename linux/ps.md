# ps
linux上进程有5种状态:
1. 运行(正在运行或在运行队列中等待)
2. 中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号)
3. 不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生)
4. 僵死(进程已终止, 但进程描述符存在, 直到父进程调用wait4()系统调用后释放)
5. 停止(进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号后停止运行运行)

1）ps a 显示现行终端机下的所有程序，包括其他用户的程序。    
2）ps -A 显示所有程序。    
3）ps c 列出程序时，显示每个程序真正的指令名称，而不包含路径，参数或常驻服务的标示。    
4）ps -e 此参数的效果和指定"A"参数相同。    
5）ps e 列出程序时，显示每个程序所使用的环境变量。    
6）ps f 用ASCII字符显示树状结构，表达程序间的相互关系。    
7）ps -H 显示树状结构，表示程序间的相互关系。    
8）ps -N 显示所有的程序，除了执行ps指令终端机下的程序之外。    
9）ps s 采用程序信号的格式显示程序状况。    
10）ps S 列出程序时，包括已中断的子程序资料。    
11）ps -t <终端机编号> 　指定终端机编号，并列出属于该终端机的程序的状况。    
12）ps u 　 以用户为主的格式来显示程序状况。    
13）ps x 　 显示所有程序，不以终端机来区分。    
14）ps -l 较长，较详细的显示该的信息    

```shell
 ps -lA |more
F S UID PID PPID C PRI NI ADDR SZ WCHAN TTY TIME CMD
4 S 0 1 0 0 76 0 - 1193 109952 ? 00:00:03 init
1 S 0 2 1 0 -40 - - 0 migrat ? 00:00:03 migration/0
1 S 0 3 1 0 94 19 - 0 ksofti ? 00:00:00 ksoftirqd/0
1 S 0 4 1 0 -40 - - 0 migrat ? 00:00:02 migration/1
1 S 0 5 1 0 94 19 - 0 ksofti ? 00:00:00 ksoftirqd/1
1 S 0 6 1 0 -40 - - 0 migrat ? 00:00:02 migration/2
1 S 0 7 1 0 94 19 - 0 ksofti ? 00:00:00 ksoftirqd/2
```
* 相关信息的意义：    
F 进程的标志（flag），4表示用户为超级用户    
S 进程的状态（stat），各STAT的见下文    
PID 进程的ID    
C CPU使用资源的百分比    
PRI priority(优先级)的缩写，    
NI Nice值，    
ADDR 核心功能，指出该时进程在内存的那一部分，如果是运行的进程，一般都是“-”    
SZ 用掉的内存的大小    
WCHAN 当进程是否正在运行，若为“-”表示正在运行    
TTY 登录者的终端位置    
TIME 用掉的CPU的时间    

&nbsp;&nbsp;&nbsp;&nbsp;
* CMD 所執行的指令
```shell
# ps aux |more
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
root 1 0.0 0.0 4772 564 ? S Sep22 0:03 init [3]
root 2 0.0 0.0 0 0 ? S Sep22 0:03 [migration/0]
root 3 0.0 0.0 0 0 ? SN Sep22 0:00 [ksoftirqd/0]
root 4 0.0 0.0 0 0 ? S Sep22 0:02 [migration/1]
root 5 0.0 0.0 0 0 ? SN Sep22 0:00 [ksoftirqd/1]
root 6 0.0 0.0 0 0 ? Ss+ Sep22 0:02 [migration/2]
root 7 0.0 0.0 0 0 ? SN Sep22 0:00 [ksoftirqd/2]
root 8 0.0 0.0 0 0 ? S Sep22 0:00 [migration/3]
root 9 0.0 0.0 0 0 ? SN Sep22 0:00 [ksoftirqd/3]
root 10 0.0 0.0 0 0 ? S< Sep22 0:00 [migration/4]
```    
* 上述各位解释：    
USER 进程的属主；    
PID 进程的ID；    
PPID 父进程；    
%CPU 进程占用的CPU百分比；    
%MEM 占用内存的百分比；    
NI 进程的NICE值，数值大，表示较少占用CPU时间；    
VSZ 进程使用的虚拟内存量（KB）；    
RSS 该进程占用的固定内存量（KB）（驻留中页的数量）；    
TTY 该进程在那个终端上运行（登录都终端位置），若与终端无关，则显示（？）。    
若为pts/0等，则表示由网络连接主机进程    
WCHAN 当前进程是否正在进行，若为-表示正在进行；    
START 该进程被触发启动时间；    
TIME 该进程实际使用CPU运行的时间；    
COMMAND 命令的名称和参数；    
STAT状态位常见的状态字符    
D 无法中断的休眠状态（通常 IO 的进程）；    
R 正在运行可中在队列中可过行的；    
S 处于休眠状态；    
T 停止或被追踪；    
W 进入内存交换 （从内核2.6开始无效）；    
X 死掉的进程 （基本很少见）；    
Z 僵尸进程；    
< 优先级高的进程    
N 优先级较低的进程    
L 有些页被锁进内存；    
s 进程的领导者（在它之下有子进程）；    
l 多进程的（使用 CLONE_THREAD, 类似 NPTL pthreads）；    
+ 位于后台的进程组；    

