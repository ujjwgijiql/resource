# Dubbo的安装与配置
   ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、名字服务、分布式同步、组服务等。    
  Dubbo是Alibaba开源的分布式服务框架，它最大的特点是按照分层的方式来架构，使用这种方式可以使各个层之间解耦合（或者最大限度地松耦合）。从服务模型的角度来看，Dubbo采用的是一种非常简单的模型，要么是提供方提供服务，要么是消费方消费服务，所以基于这一点可以抽象出服务提供方（Provider）和服务消费方（Consumer）两个角色。关于注册中心、协议支持、服务监控等内容。    
  
  
## Dubbo的安装与配置
这里服务器我们用的是一台Centos，在dubbo配置工作前，需要做以下准备工作    
主要是应用环境的软件与工具配置    

### 一、安装JDK
原有系统自带的版本可能较旧，需要重新安装新版本    
1、  查看原有版本    
yum list installed |grep java    
 或java -version    
 
### 二、安装Tomcat
1、下载安装tomcat,具体版本可选择    
2、 下载与解压    
```shell
[root@centos-svr-103 tomcat]#
curl -O https://archive.apache.org/dist/tomcat/tomcat-8/v8.0.30/bin/apache-tomcat-8.0.30.tar.gz
[root@centos-svr-103 tomcat]# tar -zxvf apache-tomcat-8.0.30.tar.gz
```    
3、修改tomcat的默认端口8080，修改方法如下，打到conf下的文件 server.xml    
因zookeeper会用到8080的端口，所以为了端口不冲突，可把Tomcat的端口改一下    
当然你若改了zookeeper的端口，这里就不用改    

### 三、下载安装zookeeper 

### 四、dubbo 安装和配置
__1、  下载源码打包__    
官网提供的源码包，最新的版本已经没有dubbo-admin，要到2.6.0的分支上下载。    
地址：https://github.com/alibaba/dubbo    
下载到windows系统中，解压后，打开cmd命令窗口，进入源码文件所在总目录下    
利用maven命令打包：    
```shell
mvn install -Dmaven.test.skip=true
```
命令执行成功结束后，在  dubbo-admin 目录下会生成target文件夹，文件夹下会出现一个war包    
若未成功生成war包，可进入dubbo-admin目录，执行命令mvn package -Dmaven.test.skip ，把dubbo-admin打成war包    

__2、  解压__    
将上面的dubbo的war打包文件复制到centos服务器tomcat下webapps目录,启动一下Tomcat,让它把war解压了,再把 Tomcat给关了。   
启动tomcat    
```shell
./startup.sh
```    

关闭tomcat服务
```shell
./shutdown.sh
```    

__3、修改dubbo.properties__
在\webapps\dubbo-admin-2.6.0-SNAPSHOT\WEB-INF\下，编辑dubbo.properties文件
```text
dubbo.registry.address=zookeeper://127.0.0.1:2181  
dubbo.admin.root.password=root  
dubbo.admin.guest.password=guest  
```    
这里的127.0.0.1对应的是自己的电脑IP，如果zookeeper也在同一台电脑上，只要这么写就行。一般情况下都是不需要改的，因为初始都是本地的IP地址。但还是看看比较安全

__4. 访问__
这下全部都配置好了，首先，一定要先启动zookeeper启动后再去启动tomcat！    
http://192.168.4.78:8080/

