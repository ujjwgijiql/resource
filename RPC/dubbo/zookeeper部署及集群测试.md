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

## 测试
__在 c1 上连接 c1 上的 zookeeper__    
/var/local/server/zookeeper/bin/zkCli.sh -server c1:2181    
```shell
[root@sso conf]# /var/local/server/zookeeper/bin/zkCli.sh -server c1:2181
Connecting to c1:2181
2016-08-25 10:06:30,235 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.6-1569965, built on 02/20/2014 09:09 GMT
2016-08-25 10:06:30,244 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=sso.xxx.com
2016-08-25 10:06:30,244 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.7.0_79
2016-08-25 10:06:30,250 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
2016-08-25 10:06:30,251 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/usr/local/jdk1.7.0_79/jre
2016-08-25 10:06:30,251 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/var/local/server/zookeeper/bin/../build/classes:/var/local/server/zookeeper/bin/../build/lib/*.jar:/var/local/server/zookeeper/bin/../lib/slf4j-log4j12-1.6.1.jar:/var/local/server/zookeeper/bin/../lib/slf4j-api-1.6.1.jar:/var/local/server/zookeeper/bin/../lib/netty-3.7.0.Final.jar:/var/local/server/zookeeper/bin/../lib/log4j-1.2.16.jar:/var/local/server/zookeeper/bin/../lib/jline-0.9.94.jar:/var/local/server/zookeeper/bin/../zookeeper-3.4.6.jar:/var/local/server/zookeeper/bin/../src/java/lib/*.jar:/var/local/server/zookeeper/bin/../conf:
2016-08-25 10:06:30,251 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
2016-08-25 10:06:30,252 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
2016-08-25 10:06:30,252 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
2016-08-25 10:06:30,252 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
2016-08-25 10:06:30,252 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
2016-08-25 10:06:30,253 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=3.10.0-327.10.1.el7.x86_64
2016-08-25 10:06:30,253 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=root
2016-08-25 10:06:30,253 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/root
2016-08-25 10:06:30,254 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/var/local/server/zookeeper/conf
2016-08-25 10:06:30,257 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=c1:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@194d62f1
Welcome to ZooKeeper!
2016-08-25 10:06:30,335 [myid:] - INFO  [main-SendThread(sso.xxx.com:2181):ClientCnxn$SendThread@975] - Opening socket connection to server sso.xxx.com/192.168.1.80:2181. Will not attempt to authenticate using SASL (unknown error)
JLine support is enabled
2016-08-25 10:06:30,349 [myid:] - INFO  [main-SendThread(sso.xxx.com:2181):ClientCnxn$SendThread@852] - Socket connection established to sso.xxx.com/192.168.1.80:2181, initiating session
2016-08-25 10:06:30,412 [myid:] - INFO  [main-SendThread(sso.xxx.com:2181):ClientCnxn$SendThread@1235] - Session establishment complete on server sso.xxx.com/192.168.1.80:2181, sessionid = 0x156bf70d7580000, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: c1:2181(CONNECTED) 0]
```    

__添加一个根节点目录zookeeper_project__    
create /project zookeeper_project    
```shell
[zk: c1:2181(CONNECTED) 0] create /project  zookeeper_project
Created /project
[zk: c1:2181(CONNECTED) 1] get /project
zookeeper_project
cZxid = 0x2
ctime = Thu Aug 25 10:09:09 CST 2016
mZxid = 0x2
mtime = Thu Aug 25 10:09:09 CST 2016
pZxid = 0x2
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 17
numChildren = 0
```    

此时说明单机配置成功    

## 多机集群
取消zoo.cfg中server.2和server.3的注释，最后zoo.cfg配置文件如下：    
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
在c2和c3中执行类似的操作

__多机联合测试__
c1运行结果：    
```shell
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: leader
```    

c2运行结果：    
```shell
[root@cas zookeeper]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```    

c3运行结果：    
```shell
[root@localhost zookeeper]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```    

## 同步测试
c1上执行：    
create /c1project c1projecttest和get /c1project    
```shell
[zk: c1:2181(CONNECTED) 2] create /c1project c1projecttest
Created /c1project
[zk: c1:2181(CONNECTED) 1] get /c1project
c1projecttest
cZxid = 0x100000013
ctime = Thu Aug 25 11:03:49 CST 2016
mZxid = 0x100000013
mtime = Thu Aug 25 11:03:49 CST 2016
pZxid = 0x100000013
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 13
numChildren = 0
```    

c2上查看同步结果：    
get /c1project
```shell
[zk: c2:2181(CONNECTED) 0] get /c1project
c1projecttest
cZxid = 0x100000013
ctime = Thu Aug 25 11:03:49 CST 2016
mZxid = 0x100000013
mtime = Thu Aug 25 11:03:49 CST 2016
pZxid = 0x100000013
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 13
numChildren = 0
```    

c3上查看同步结果：    
```shell
[zk: c3:2181(CONNECTED) 4] get /c1project
c1projecttest
cZxid = 0x100000013
ctime = Thu Aug 25 11:03:49 CST 2016
mZxid = 0x100000013
mtime = Thu Aug 25 11:03:49 CST 2016
pZxid = 0x100000013
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 13
numChildren = 0
```    
此时说明zookeeper集群配置成功。

## 从节点主动升级和接管成为主节点
leader和follower的自动切换，前面我们知道c1是leader，当leader down掉后是否有从节点升级为leader。    
c1上执行    
```shell
/var/local/server/zookeeper/bin/zkServer.sh stop
```    

c2上查看状态
```shell
[root@cas ~]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```    
可以发现c2仍然为从节点    

再看看c3    
```shell
[root@localhost ~]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: leader
```    
此时c3升级为了主节点

再次启动c1，并查看状态
```shell
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh start
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@sso conf]#
[root@sso conf]# /var/local/server/zookeeper/bin/zkServer.sh status
JMX enabled by default
Using config: /var/local/server/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```    
发现c1作为了从节点。


