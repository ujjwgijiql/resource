# zookeeper部署及集群测试
## 环境
__三台测试机__    

* 操作系统: centos7; hostname: c1; ip: 192.168.1.80
* 操作系统: centos7; hostname: c2; ip: 192.168.1.81
* 操作系统: centos7; hostname: c3; ip: 192.168.1.82

备注
> 注意要关闭防火墙    

查看磁盘状况    
为了避免磁盘使用不均而导致系统问题，因而先查看磁盘的分布情况。    
```shell
root@sso conf]# df -lh
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   50G   18G   33G  36% /
devtmpfs                 7.8G     0  7.8G   0% /dev
tmpfs                    7.8G     0  7.8G   0% /dev/shm
tmpfs                    7.8G   65M  7.7G   1% /run
tmpfs                    7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/mapper/centos-var   900G  529M  900G   1% /var
/dev/mapper/centos-home   66G   12G   55G  18% /home
/dev/sda1                497M  168M  330M  34% /boot
tmpfs                    1.6G     0  1.6G   0% /run/user/0
[root@sso conf]#
```    
/var目录共900G，只使用了1%不到，因此可以将后续的所有文件和程序存放在此目录下。当然，这要根据你的实际情况而定。    

## zookeeper安装
__安装：__  
```shell
 mkdir -p /var/download && cd /var/download
 wget -c http://www.eu.apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
 tar zxvf zookeeper-3.4.6.tar.gz
 mkdir -p /var/local/server
 mv zookeeper-3.4.6 /var/local/server/zookeeper
 cd /var/local/server/zookeeper/conf
 touch zoo.cfg
```    
__编辑zoo.cfg中加入如下内容：__    
```text
tickTime=2000
dataDir=/var/data/zookeeper
dataLogDir=/var/local/server/zookeeper/log
clientPort=2181
initLimit=5
syncLimit=2
server.1=c1:2888:3888
server.2=c2:2888:3888
server.3=c3:2888:3888
```    
保存    
&nbsp;&nbsp;&nbsp;&nbsp;    
__创建数据目录和日志目录：__    
```shell
mkdir -p /var/data/zookeeper
mkdir -p /var/local/server/zookeeper/log
```    
如不创建启动时会报如下异常：
```shell
root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
```    
__创建myid文件， id 与 zoo.cfg 中的序号对应__    
```shell
echo 1 > /var/data/zookeeper/myid
```    
注意：    
* 如果是c2和c3中进行相应的修改    
* c2上应改为：echo 2 > /var/data/zookeeper/myid    
* c3上应改为：echo 3 > /var/data/zookeeper/myid    

__配置hosts文件：编辑/etc/hosts，加入如下内容__
```shell
192.168.1.80 c1
192.168.1.81 c2
192.168.1.82 c3
```    

## 常用命令    
__启动__
```shell
/var/local/server/zookeeper/bin/zkServer.sh start
```    
__查看状态__
```shell
/var/local/server/zookeeper/bin/zkServer.sh status
```    
__重启__
```shell
/var/local/server/zookeeper/bin/zkServer.sh restart
```    
__关闭__
```shell
/var/local/server/zookeeper/bin/zkServer.sh stop
```    
__在其中一台用客户端连接__
```shell
/var/local/server/zookeeper/bin/zkCli.sh -server c1:2181
```    

## 操作实例：
* 启动：/var/local/server/zookeeper/bin/zkServer.sh start
```shell
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh start
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```    
* 查看状态：/var/local/server/zookeeper/bin/zkServer.sh status    
```shell
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
```    
此时发现zookeeper并没有正常运行。    
查看日志more zookeeper.out，发现如下异常：
```shell
2016-08-25 09:55:54,088 [myid:1] - WARN  [WorkerSender[myid=1]:QuorumCnxManager@382] - Cannot open channel to 2 at election address c2/192.168.1.81:3888
java.net.NoRouteToHostException: No route to host
        at java.net.PlainSocketImpl.socketConnect(Native Method)
        at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:339)
        at java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:200)
        at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:182)
        at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
        at java.net.Socket.connect(Socket.java:579)
        at org.apache.zookeeper.server.quorum.QuorumCnxManager.connectOne(QuorumCnxManager.java:368)
        at org.apache.zookeeper.server.quorum.QuorumCnxManager.toSend(QuorumCnxManager.java:341)
        at org.apache.zookeeper.server.quorum.FastLeaderElection$Messenger$WorkerSender.process(FastLeaderElection.java:449)
        at org.apache.zookeeper.server.quorum.FastLeaderElection$Messenger$WorkerSender.run(FastLeaderElection.java:430)
        at java.lang.Thread.run(Thread.java:745)
2016-08-25 09:55:54,101 [myid:1] - WARN  [WorkerSender[myid=1]:QuorumCnxManager@382] - Cannot open channel to 3 at election address c3/192.168.1.82:3888
java.net.ConnectException: Connection refused
        at java.net.PlainSocketImpl.socketConnect(Native Method)
        at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:339)
        at java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:200)
        at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:182)
        at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
        at java.net.Socket.connect(Socket.java:579)
        at org.apache.zookeeper.server.quorum.QuorumCnxManager.connectOne(QuorumCnxManager.java:368)
        at org.apache.zookeeper.server.quorum.QuorumCnxManager.toSend(QuorumCnxManager.java:341)
        at org.apache.zookeeper.server.quorum.FastLeaderElection$Messenger$WorkerSender.process(FastLeaderElection.java:449)
        at org.apache.zookeeper.server.quorum.FastLeaderElection$Messenger$WorkerSender.run(FastLeaderElection.java:430)
        at java.lang.Thread.run(Thread.java:745)
2016-08-25 09:55:54,287 [myid:1] - WARN  [QuorumPeer[myid=1]/0:0:0:0:0:0:0:0:2181:QuorumCnxManager@382] - Cannot open channel to 2 at election address c2/192.168.1.81:3888
java.net.NoRouteToHostException: No route to host
        at java.net.PlainSocketImpl.socketConnect(Native Method)
        at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:339)
        at java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:200)
        at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:182)
        at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
        at java.net.Socket.connect(Socket.java:579)
        at org.apache.zookeeper.server.quorum.QuorumCnxManager.connectOne(QuorumCnxManager.java:368)
        at org.apache.zookeeper.server.quorum.QuorumCnxManager.connectAll(QuorumCnxManager.java:402)
        at org.apache.zookeeper.server.quorum.FastLeaderElection.lookForLeader(FastLeaderElection.java:840)
        at org.apache.zookeeper.server.quorum.QuorumPeer.run(QuorumPeer.java:762)
```    
这是由于2、3号机器没有正常配置导致。可以在zoo.cfg中将其注释后再次尝试。    
```shell
tickTime=2000
dataDir=/var/data/zookeeper
dataLogDir=/var/local/server/zookeeper/log
clientPort=2181
initLimit=5
syncLimit=2
server.1=c1:2888:3888
#server.2=c2:2888:3888
#server.3=c3:2888:3888
```
正常启动效果：    
```shell
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh start
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: standalone
```    
