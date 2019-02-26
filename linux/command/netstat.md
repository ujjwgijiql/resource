# netstat
在linux一般使用netstat 来查看系统端口使用情况步。    
netstat命令是一个监控TCP/IP网络的非常有用的工具，它可以显示路由表、实际的网络连接以及每一个网络接口设备。    
netstat命令的功能是显示网络连接、路由表和网络接口信息，可以让用户得知目前都有哪些网络连接正在运作。    
该命令的一般格式为：netstat [选项]    
```shell
netstat -nat |awk ‘{print $6}’|sort|uniq -c|sort -nr
```
## 1.netstat命令详解
常用的是 netstat -tnl | grep 443 （查看443端口是否被占用），如果有当前是root用户，用netstat -pnl | grep 443 （还可显示出占用本机443端口的进程PID）。    
netstat    
功能说明：显示网络状态。    
语　　法：netstat [-acCeFghilMnNoprstuvVwx] [-A<网络类型>][--ip]    
补充说明：利用netstat指令可让你得知整个Linux系统的网络情况。    
参　　数：    
* -a　或–all　　　　　　　　　　　 显示所有连线中的Socket。    
* -A　　　　　　　　　　　　　　　 <网络类型>或–<网络类型> 列出该网络类型连线中的相关地址。
* -c　或–continuous　　　　　　　 持续列出网络状态。
* -C　或–cache　　　　　　　　　　显示路由器配置的快取信息。
* -e　或–extend　　　　　　　　　 显示网络其他相关信息。
* -F　或 –fib　　　　　　　　　　 显示FIB。
* -g　或–groups　　　　　　　　　 显示多重广播功能群组组员名单。
* -h　或–help　　　　　　　　　　 在线帮助。
* -i　或–interfaces　　　　　　　显示网络界面信息表单。
* -l　或–listening　　　　　　　 显示监控中的服务器的Socket。
* -M　或–masquerade　　　　　　　显示伪装的网络连线。
* -n　或–numeric　　　　　　　　 直接使用IP地址，而不通过域名服务器。
* -N　或–netlink或–symbolic　　 显示网络硬件外围设备的符号连接名称。
* -o　或–timers　　　　　　　　　显示计时器。
* -p　或–programs　　　　　　　　显示正在使用Socket的程序识别码和程序名称。
* -r　或–route　　　　　　　　　 显示 Routing Table。
* -s　或–statistice　　　　　　 显示网络工作信息统计表。
* -t　或–tcp　　　　　　　　　　 显示TCP 传输协议的连线状况。
* -u　或–udp　　　　　　　　　　 显示UDP传输协议的连线状况。
* -v　或–verbose　　　　　　　　 显示指令执行过程。
* -V　或–version　　　　　　　　 显示版本信息。
* -w　或–raw　　　　　　　　　　 显示RAW传输协议的连线状况。
* -x　或–unix　　　　　　　　　　此参数的效果和指定”-A unix”参数相同。
* –ip　或–inet　　　　　　　　　 此参数的效果和指定”-A inet”参数相同。    
&nbsp;&nbsp;&nbsp;&nbsp;

### 实用命令实例
__1. 列出所有端口 (包括监听和未监听的)__
* 列出所有端口 netstat -a
```shell
# netstat -a | more
 Active Internet connections (servers and established)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:30037         *:*                     LISTEN
 udp        0      0 *:bootpc                *:*
 
Active UNIX domain sockets (servers and established)
 Proto RefCnt Flags       Type       State         I-Node   Path
 unix  2      [ ACC ]     STREAM     LISTENING     6135     /tmp/.X11-unix/X0
 unix  2      [ ACC ]     STREAM     LISTENING     5140     /var/run/acpid.socket
```
* 列出所有 tcp 端口 netstat -at
```shell
# netstat -at
 Active Internet connections (servers and established)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:30037         *:*                     LISTEN
 tcp        0      0 localhost:ipp           *:*                     LISTEN
 tcp        0      0 *:smtp                  *:*                     LISTEN
 tcp6       0      0 localhost:ipp           [::]:*                  LISTEN
```
* 列出所有 udp 端口 netstat -au
```shell
# netstat -au
 Active Internet connections (servers and established)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 udp        0      0 *:bootpc                *:*
 udp        0      0 *:49119                 *:*
 udp        0      0 *:mdns                  *:*
```
&nbsp;&nbsp;&nbsp;&nbsp;
__2. 列出所有处于监听状态的 Sockets__
* 只显示监听端口 netstat -l
```shell
# netstat -l
 Active Internet connections (only servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:ipp           *:*                     LISTEN
 tcp6       0      0 localhost:ipp           [::]:*                  LISTEN
 udp        0      0 *:49119                 *:*
```

* 只列出所有监听 tcp 端口 netstat -lt
```shell
# netstat -lt
 Active Internet connections (only servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:30037         *:*                     LISTEN
 tcp        0      0 *:smtp                  *:*                     LISTEN
 tcp6       0      0 localhost:ipp           [::]:*                  LISTEN
```

*  只列出所有监听 udp 端口 netstat -lu
```shell
# netstat -lu
 Active Internet connections (only servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 udp        0      0 *:49119                 *:*
 udp        0      0 *:mdns                  *:*
```

* 只列出所有监听 UNIX 端口 netstat -lx
```shell
# netstat -lx
 Active UNIX domain sockets (only servers)
 Proto RefCnt Flags       Type       State         I-Node   Path
 unix  2      [ ACC ]     STREAM     LISTENING     6294     private/maildrop
 unix  2      [ ACC ]     STREAM     LISTENING     6203     public/cleanup
 unix  2      [ ACC ]     STREAM     LISTENING     6302     private/ifmail
 unix  2      [ ACC ]     STREAM     LISTENING     6306     private/bsmtp
```
&nbsp;&nbsp;&nbsp;&nbsp;
__3. 显示每个协议的统计信息__
* 显示所有端口的统计信息 netstat -s
```shell
# netstat -s
 Ip:
 11150 total packets received
 1 with invalid addresses
 0 forwarded
 0 incoming packets discarded
 11149 incoming packets delivered
 11635 requests sent out
 Icmp:
 0 ICMP messages received
 0 input ICMP message failed.
 Tcp:
 582 active connections openings
 2 failed connection attempts
 25 connection resets received
 Udp:
 1183 packets received
 4 packets to unknown port received.
 .....
```

* 显示 TCP 或 UDP 端口的统计信息 netstat -st 或 -su
```shell
# netstat -st 
# netstat -su
```
&nbsp;&nbsp;&nbsp;&nbsp;
__4. 在 netstat 输出中显示 PID 和进程名称 netstat -p__
netstat -p 可以与其它开关一起使用，就可以添加 “PID/进程名称” 到 netstat 输出中，这样 debugging 的时候可以很方便的发现特定端口运行的程序。
```shell
# netstat -pt
 Active Internet connections (w/o servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
 tcp        1      0 ramesh-laptop.loc:47212 192.168.185.75:www        CLOSE_WAIT  2109/firefox
 tcp        0      0 ramesh-laptop.loc:52750 lax:www ESTABLISHED 2109/firefox
```
&nbsp;&nbsp;&nbsp;&nbsp;
__5. 在 netstat 输出中不显示主机，端口和用户名 (host, port or user)__
当你不想让主机，端口和用户名显示，使用 netstat -n。将会使用数字代替那些名称。    
同样可以加速输出，因为不用进行比对查询。    
```shell
# netstat -an
```
如果只是不想让这三个名称中的一个被显示，使用以下命令
```shell
# netsat -a --numeric-ports
# netsat -a --numeric-hosts
# netsat -a --numeric-users
```
&nbsp;&nbsp;&nbsp;&nbsp;
__6. 持续输出 netstat 信息__
netstat 将每隔一秒输出网络信息。
```shell
# netstat -c
 Active Internet connections (w/o servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 ramesh-laptop.loc:36130 101-101-181-225.ama:www ESTABLISHED
 tcp        1      1 ramesh-laptop.loc:52564 101.11.169.230:www      CLOSING
 tcp        0      0 ramesh-laptop.loc:43758 server-101-101-43-2:www ESTABLISHED
 tcp        1      1 ramesh-laptop.loc:42367 101.101.34.101:www      CLOSING
 ^C
```
&nbsp;&nbsp;&nbsp;&nbsp;
__7. 显示系统不支持的地址族 (Address Families)__
```shell
netstat --verbose
```
在输出的末尾，会有如下的信息
```shell
netstat: no support for `AF IPX' on this system.
netstat: no support for `AF AX25' on this system.
netstat: no support for `AF X25' on this system.
netstat: no support for `AF NETROM' on this system.
```
&nbsp;&nbsp;&nbsp;&nbsp;
__8. 显示核心路由信息 netstat -r__
```shell
# netstat -r
 Kernel IP routing table
 Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
 192.168.1.0     *               255.255.255.0   U         0 0          0 eth2
 link-local      *               255.255.0.0     U         0 0          0 eth2
 default         192.168.1.1     0.0.0.0         UG        0 0          0 eth2
```
注意： 使用 netstat -rn 显示数字格式，不查询主机名称。
&nbsp;&nbsp;&nbsp;&nbsp;
__9. 找出程序运行的端口__
并不是所有的进程都能找到，没有权限的会不显示，使用 root 权限查看所有的信息。
```shell
# netstat -ap | grep ssh
 tcp        1      0 dev-db:ssh           101.174.100.22:39213        CLOSE_WAIT  -
 tcp        1      0 dev-db:ssh           101.174.100.22:57643        CLOSE_WAIT  -
```
找出运行在指定端口的进程
```shell
# netstat -an | grep ':80'
```
&nbsp;&nbsp;&nbsp;&nbsp;
__10. 显示网络接口列表__
```shell
# netstat -i
 Kernel Interface table
 Iface   MTU Met   RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
 eth0       1500 0         0      0      0 0             0      0      0      0 BMU
 eth2       1500 0     26196      0      0 0         26883      6      0      0 BMRU
 lo        16436 0         4      0      0 0             4      0      0      0 LRU
 ```
 显示详细信息，像是 ifconfig 使用 netstat -ie:
 ```shell
 # netstat -ie
 Kernel Interface table
 eth0      Link encap:Ethernet  HWaddr 00:10:40:11:11:11
 UP BROADCAST MULTICAST  MTU:1500  Metric:1
 RX packets:0 errors:0 dropped:0 overruns:0 frame:0
 TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
 collisions:0 txqueuelen:1000
 RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
 Memory:f6ae0000-f6b00000
 ```
&nbsp;&nbsp;&nbsp;&nbsp;
## 2.网络连接状态详解
共有12中可能的状态，前面11种是按照TCP连接建立的三次握手和TCP连接断开的四次挥手过程来描述的。    
1)、LISTEN:首先服务端需要打开一个socket进行监听，状态为LISTEN./* The socket is listening for incoming connections. 侦听来自远方TCP端口的连接请求 */

2)、 SYN_SENT:客户端通过应用程序调用connect进行active open.于是客户端tcp发送一个SYN以请求建立一个连接.之后状态置为SYN_SENT./*The socket is actively attempting to establish a connection. 在发送连接请求后等待匹配的连接请求 */

3)、 SYN_RECV:服务端应发出ACK确认客户端的 SYN,同时自己向客户端发送一个SYN. 之后状态置为SYN_RECV/* A connection request has been received from the network. 在收到和发送一个连接请求后等待对连接请求的确认 */

4)、ESTABLISHED: 代表一个打开的连接，双方可以进行或已经在数据交互了。/* The socket has an established connection. 代表一个打开的连接，数据可以传送给用户 */

5)、 FIN_WAIT1:主动关闭(active close)端应用程序调用close，于是其TCP发出FIN请求主动关闭连接，之后进入FIN_WAIT1状态./* The socket is closed, and the connection is shutting down. 等待远程TCP的连接中断请求，或先前的连接中断请求的确认 */

6)、CLOSE_WAIT:被动关闭(passive close)端TCP接到FIN后，就发出ACK以回应FIN请求(它的接收也作为文件结束符传递给上层应用程序),并进入CLOSE_WAIT./* The remote end has shut down, waiting for the socket to close. 等待从本地用户发来的连接中断请求 */

7)、FIN_WAIT2:主动关闭端接到ACK后，就进入了 FIN-WAIT-2 ./* Connection is closed, and the socket is waiting for a shutdown from the remote end. 从远程TCP等待连接中断请求 */

8)、LAST_ACK:被动关闭端一段时间后，接收到文件结束符的应用程 序将调用CLOSE关闭连接。这导致它的TCP也发送一个 FIN,等待对方的ACK.就进入了LAST-ACK ./* The remote end has shut down, and the socket is closed. Waiting for acknowledgement. 等待原来发向远程TCP的连接中断请求的确认 */

9)、TIME_WAIT:在主动关闭端接收到FIN后，TCP 就发送ACK包，并进入TIME-WAIT状态。/* The socket is waiting after close to handle packets still in the network.等待足够的时间以确保远程TCP接收到连接中断请求的确认 */

10)、CLOSING: 比较少见./* Both sockets are shut down but we still don’t have all our data sent. 等待远程TCP对连接中断的确认 */

11)、CLOSED: 被动关闭端在接受到ACK包后，就进入了closed的状态。连接结束./* The socket is not being used. 没有任何连接状态 */

12)、UNKNOWN: 未知的Socket状态。/* The state of the socket is unknown. */

SYN: (同步序列编号,Synchronize Sequence Numbers)该标志仅在三次握手建立TCP连接时有效。表示一个新的TCP连接请求。
ACK: (确认编号,Acknowledgement Number)是对TCP请求的确认标志,同时提示对端系统已经成功接收所有数据。
FIN: (结束标志,FINish)用来结束一个TCP回话.但对应端口仍处于开放状态,准备接收后续数据。

PS： 在windows下有个小工具挺好的，TCPView is a Windows program that will show you detailed listings of all TCP and UDP endpoints on your system, including the local and remote addresses and state of TCP connections.见 http://technet.microsoft.com/en-us/sysinternals/bb897437 ； 当然如果要详细分析数据包，可选用sniffer、Wireshark等更强大的工具。    
&nbsp;&nbsp;&nbsp;&nbsp;

## 3.系统连接状态
__1.查看TCP连接状态__
```shell
netstat -nat |awk '{print $6}'|sort|uniq -c|sort -rn

netstat -n | awk '/^tcp/ {++S[$NF]};END {for(a in S) print a, S[a]}' 或
netstat -n | awk '/^tcp/ {++state[$NF]}; END {for(key in state) print key,”\t”,state[key]}'
netstat -n | awk '/^tcp/ {++arr[$NF]};END {for(k in arr) print k,”\t”,arr[k]}'

netstat -n |awk '/^tcp/ {print $NF}'|sort|uniq -c|sort -rn

netstat -ant | awk '{print $NF}' | grep -v '[a-z]' | sort | uniq -c
```    

__2.查找请求数前20个IP（常用于查找攻来源）：__
```shell
netstat -anlp|grep 80|grep tcp|awk '{print $5}'|awk -F: ‘{print $1}’|sort|uniq -c|sort -nr|head -n20

netstat -ant |awk '/:80/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A[i],i}' |sort -rn|head -n20
```    

__3.用tcpdump嗅探80端口的访问看看谁最高__
```shell
tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -nr |head -20
```

__4.查找较多time_wait连接__
```shell
netstat -n|grep TIME_WAIT|awk '{print $5}'|sort|uniq -c|sort -rn|head -n20
```

__5.找查较多的SYN连接__
```shell
netstat -an | grep SYN | awk '{print $5}' | awk -F: '{print $1}' | sort | uniq -c | sort -nr | more
```

__6.根据端口列进程__
```shell
netstat -ntlp | grep 80 | awk '{print $7}' | cut -d/ -f1
```    
&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;
## 网站日志分析篇1（Apache）：

__1.获得访问前10位的ip地址__
cat access.log|awk ‘{print $1}’|sort|uniq -c|sort -nr|head -10
cat access.log|awk ‘{counts[$(11)]+=1}; END {for(url in counts) print counts[url], url}’

__2.访问次数最多的文件或页面,取前20__
cat access.log|awk ‘{print $11}’|sort|uniq -c|sort -nr|head -20

__3.列出传输最大的几个exe文件（分析下载站的时候常用）__
cat access.log |awk ‘($7~/\.exe/){print $10 ” ” $1 ” ” $4 ” ” $7}’|sort -nr|head -20

__4.列出输出大于200000byte(约200kb)的exe文件以及对应文件发生次数__
cat access.log |awk ‘($10 > 200000 && $7~/\.exe/){print $7}’|sort -n|uniq -c|sort -nr|head -100

__5.如果日志最后一列记录的是页面文件传输时间，则有列出到客户端最耗时的页面__
cat access.log |awk ‘($7~/\.php/){print $NF ” ” $1 ” ” $4 ” ” $7}’|sort -nr|head -100

__6.列出最最耗时的页面(超过60秒的)的以及对应页面发生次数__
cat access.log |awk ‘($NF > 60 && $7~/\.php/){print $7}’|sort -n|uniq -c|sort -nr|head -100

__7.列出传输时间超过 30 秒的文件__
cat access.log |awk ‘($NF > 30){print $7}’|sort -n|uniq -c|sort -nr|head -20

__8.统计网站流量（G)__
cat access.log |awk ‘{sum+=$10} END {print sum/1024/1024/1024}’

__9.统计404的连接__
awk ‘($9 ~/404/)’ access.log | awk ‘{print $9,$7}’ | sort

__10. 统计http status.__
cat access.log |awk ‘{counts[$(9)]+=1}; END {for(code in counts) print code, counts[code]}'
cat access.log |awk '{print $9}'|sort|uniq -c|sort -rn

__11.蜘蛛分析__
查看是哪些蜘蛛在抓取内容。
/usr/sbin/tcpdump -i eth0 -l -s 0 -w - dst port 80 | strings | grep -i user-agent | grep -i -E 'bot|crawler|slurp|spider'

&nbsp;&nbsp;&nbsp;&nbsp;
## 网站日分析2(Squid篇）

__1.按域统计流量__
zcat squid_access.log.tar.gz| awk '{print $10,$7}' |awk 'BEGIN{FS="[ /]"}{trfc[$4]+=$1}END{for(domain in trfc){printf "%s\t%d\n",domain,trfc[domain]}}'


&nbsp;&nbsp;&nbsp;&nbsp;
## 数据库篇

__1.查看数据库执行的sql__
/usr/sbin/tcpdump -i eth0 -s 0 -l -w - dst port 3306 | strings | egrep -i 'SELECT|UPDATE|DELETE|INSERT|SET|COMMIT|ROLLBACK|CREATE|DROP|ALTER|CALL'

&nbsp;&nbsp;&nbsp;&nbsp;
## 系统Debug分析篇

__1.调试命令__
strace -p pid

__2.跟踪指定进程的PID__
gdb -p pid






