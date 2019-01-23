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
__安装__  
```shell
 mkdir -p /var/download && cd /var/download
 wget -c http://www.eu.apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
 tar zxvf zookeeper-3.4.6.tar.gz
 mkdir -p /var/local/server
 mv zookeeper-3.4.6 /var/local/server/zookeeper
 cd /var/local/server/zookeeper/conf
 touch zoo.cfg
```    
编辑zoo.cfg中加入如下内容：    
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
创建数据目录和日志目录：    
```shell
mkdir -p /var/data/zookeeper
mkdir -p /var/local/server/zookeeper/log
```    
