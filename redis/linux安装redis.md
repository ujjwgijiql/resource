# linux安装redis 完整步骤

### 1、 获取redis资源
```shell
# wget http://download.redis.io/releases/redis-5.0.4.tar.gz
```

### 2、 解压
```shell
# tar xzvf redis-5.0.4.tar.gz
```

### 3、 安装
```shell
# cd redis-5.0.4
# make
# cd src
# make install PREFIX=/usr/share/redis-5.0.4
```

### 4、 移动配置文件到安装目录下
```shell
# cd ..
# mkdir /usr/share/redis-5.0.4/etc
# mv redis.conf /usr/share/redis-5.0.4/etc
```

### 5、 配置redis为后台启动
```shell
# vi /usr/share/redis-5.0.4/etc/redis.conf
```
将daemonize no 改成daemonize yes  
&nbsp;&nbsp;

设置密码：  
\# requirepass foobared  
requirepass 123   指定密码123
&nbsp;&nbsp;

### 6、 将redis加入到开机启动
```shell
# vi /etc/rc.local
```
在里面添加内容：  
/usr/share/redis-5.0.4/bin/redis-server /usr/share/redis-5.0.4/etc/redis.conf  
(意思就是开机调用这段开启redis的命令)  
&nbsp;&nbsp;

### 7、 开启redis
```shell
# /usr/share/redis-5.0.4/bin/redis-server /usr/share/redis-5.0.4/etc/redis.conf 
```
&nbsp;&nbsp;  

### 8、 /usr/local/redis/bin目录下的几个文件是什么
* redis-benchmark：redis性能测试工具  
* redis-check-aof：检查aof日志的工具  
* redis-check-dump：检查rdb日志的工具  
* redis-cli：连接用的客户端  
* redis-server：redis服务进程  
&nbsp;&nbsp;  
&nbsp;&nbsp;  



# 常用命令
* redis-server /usr/share/redis-5.0.4/etc/redis.conf // 启动redis  
* pkill redis  // 停止redis  
* /usr/share/redis-5.0.4/bin/redis-cli shutdown   // 停止redis   
* /usr/share/redis-5.0.4/bin/redis-cli   // 客户端连接
### 卸载redis：
* rm -rf /usr/share/redis-5.0.4 // 删除安装目录  
* rm -rf /usr/bin/redis-* // 删除所有redis相关命令脚本  
* rm -rf /root/download/redis-5.0.4 // 删除redis解压文件夹
