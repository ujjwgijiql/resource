# java CPU 100% 排查    
一个应用占用CPU很高，除了确实是计算密集型应用之外，通常原因都是出现了死循环。    
* 根据top命令，发现PID为***的Java进程占用CPU高达200%，出现故障。    
* 通过ps aux | grep PID 命令，可以进一步确定是tomcat进程出现了问题。但是，怎么定位到具体线程或者代码呢？    
* 首先显示线程列表:    
`shell
ps -mp pid -o THREAD,tid,time
`    
找到了耗时最高的线程，占用CPU的时间！    

* 其次将需要的线程ID转换为16进制格式：    
`shell
printf "%x\n" tid
`    
* 最后打印线程的堆栈信息：    
`shell
jstack pid |grep tid -A 30
`  
找到出现问题的代码了！

* 最后，总结下排查CPU故障的方法和技巧有哪些：    

1、top命令：Linux命令。可以查看实时的CPU使用情况。也可以查看最近一段时间的CPU使用情况。    

2、PS命令：Linux命令。强大的进程状态监控命令。可以查看进程以及进程中线程的当前CPU使用情况。属于当前状态的采样数据。    

3、jstack：Java提供的命令。可以查看某个进程的当前线程栈运行情况。根据这个命令的输出可以定位某个进程的所有线程的当前运行状态、运行代码，以及是否死锁等等。    

4、pstack：Linux命令。可以查看某个进程的当前线程栈运行情况。    


引用：https://www.cnblogs.com/lishijia/p/5549980.html
