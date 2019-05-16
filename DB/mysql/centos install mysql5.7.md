# centos 安装 mysql-5.7.26-linux-glibc2.12-x86_64.tar.gz

### 1、 卸载Linux系统上自带的mysql插件（old版本）
查找mysql相关安装：
```shell
# rpm -qa|grep -i mysql
```
可能会出现以下的一个或多个，没有更好，说明你的系统很干净，但是以防万一，不管怎样下面的操作还是检查一变
```shell
perl-DBD-MySQL-4.013-3.el6.x86_64
mysql-libs-5.1.71-1.el6.x86_64
qt-mysql-4.6.2-26.el6_4.x86_64
mysql-5.1.71-1.el6.x86_64
mysql-server-5.1.71-1.el6.x86_64
```
如果出现了上面的一个或多个，也不用担心，使用卸载命令，有几个干掉几个！

卸载命令：rpm –ev {包名}，如：
```shell
# rpm -ev mysql-5.1.71-1.el6.x86_64
```
如果是centos7，那么默认会安装一个mysql的分支产品mariadb，必须先卸载此分支产品
```shell
# rpm -qa | grep mariadb
mariadb-libs-5.5.56-2.el7.x86_64
```
拷贝找到的列表，一个个卸载，如
```shell
# rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
```


查找老版本mysql相关的安装目录命令：
```shell
# find / -name mysql
/etc/selinux/targeted/active/modules/100/mysql
/usr/lib64/mysql
```
若查找到相关目录使用命令：rm –rf {目录名}
```shell
# rm -rf /etc/selinux/targeted/active/modules/100/mysql/
# rm -rf /usr/lib64/mysql/
```
最后使用命令：rpm -qa|grep -i mysql，重新检查一遍系统中是否安装mysql。
```shell
# rpm -qa|grep -i mysql
```
&nbsp;&nbsp;

### 2、 安装包处理
__解压到当前目录:__  
```shell
# tar -xzvf mysql-advanced-5.7.26-linux-glibc2.12-x86_64.tar.gz
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/myisam_ftdump
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/myisamchk
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/myisamlog
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/myisampack
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_client_test_embedded
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_config_editor
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_embedded
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_install_db
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_plugin
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_secure_installation
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_ssl_rsa_setup
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_tzinfo_to_sql
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysql_upgrade
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqladmin
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlbinlog
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlcheck
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqldump
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlimport
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlpump
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlshow
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlslap
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqltest_embedded
mysql-advanced-5.7.26-linux-glibc2.12-x86_64/bin/mysqlxtest
...
```

__mysql主目录处理__  
移动文件到/usr/local/mysql:
```shell
# mv mysql-5.7.23-linux-glibc2.12-x86_64 /usr/local/mysql
# cd/usr/local/mysql
# mkdir data
```

__主目录权限处理__  
查看组和用户情况
```shell
# cat /etc/group | grep mysql
# cat /etc/passwd |grep mysql
```
若存在，则删除原mysql用户：
```shell
# userdel -r mysql
```
会删除其对应的组和用户。  
再查看就会发现没有，说明你已经删掉了  
创建mysql组和mysql用户  
```shell
# groupadd mysql
# useradd -r -g mysql mysql
# chown -R mysql:mysql /usr/local/mysql
```
&nbsp;&nbsp;

### 创建配置文件及相关目录
修改配置文件：/etc/my.cnf，配置不对的话,后面初始化不全,会拿不到默认密码。
```shell
# vim /etc/my.cnf
```
修改内容:
```shell
basedir=/usr/local/mysql
datadir=/opt/mysql/data
port = 3306
socket=/opt/mysql/mysql.sock

symbolic-links=0
log-error=/opt/logs/mysql/mysqld.log
pid-file=/opt/mysql/mysqld/mysqld.pid
sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'
[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4

[mysqld]
log-bin=mysql-bin 
binlog-format=ROW 
server_id=1 
max_connections=1000

init_connect='SET collation_connection = utf8mb4_unicode_ci'
init_connect='SET NAMES utf8mb4'
character-set-server=utf8
collation-server=utf8mb4_unicode_ci
skip-character-set-client-handshake
```
:wq! 保存退出。  
&nbsp;&nbsp;

创建文件/opt/mysql/mysql.sock：设置用户组及用户，授权  
```shell
# cd /opt/mysql
# touch mysql.sock
# chown mysql:mysql mysql.sock
# chmod 755 mysql.sock
```

创建文件/opt/mysql/mysqld/mysqld.pid：
```shell
# mkdir mysqld
# cd mysqld
# touch mysqld.pid
# cd ..
# chown -R mysql:mysql mysqld
# cd mysqld
# chmod 755 mysqld.pid
```

创建文件/opt/logs/mysql/mysqld.log：
```shell
# touch /opt/logs/mysql/mysqld.log
# chown -R mysql:mysql /opt/logs/mysql
# cd /opt/logs/mysql/
# chmod 755 mysqld.log
```
&nbsp;&nbsp;

### 安装和初始化数据库
进入bin目录:
```shell
# cd /usr/local/mysql/bin/
```
初始化数据库:
```sehll
# ./mysqld --initialize --user=mysql --basedir=/usr/local/mysql--datadir=/opt/mysql/data
```
注：这时会出现错误
```shell
./mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
```

解决方案：
```shell
# yum install -y libaio
 ```
再执行
```shell
# ./mysqld --initialize --user=mysql --basedir=/usr/local/mysql--datadir=/opt/mysql/data
 ```
 
 安全启动:
 ```shell
 # ./mysqld_safe --user=mysql &
 ```
 
 是否启动成功，可以通过查看mysql进程
 ```shell
 # ps -ef | grep mys
 ```
 
 默认密码在mysqld.log日志里, 找到后保存到安全的地方:
 ```shell
 # cat /var/log/mysqld.log
 ```
 
 其中root@localhost: 后面的就是默认密码,后面登录用.(如果找不到可能默认是空,登录时密码直接回车,否则可能安装有问题)  
进入bin目录:
```shell
# cd /usr/local/mysql/bin/
```

登录mysql:
```shell
# ./mysql -u root -p
```
拷贝或者输入mysqld.log中获得的默认密码，即可进入mysql命令客户端。  

但是，若输入相关命令，则会提示你修改用户密码（注意后面一定要加；）。  
```shell
mysql> show databases;
```
假设密码修改为xh2018
```shell
mysql> set password=password("xh2018");
```

* 设置远程登录权限
```shell
mysql> grant all privileges on *.* to 'root'@'%' identified by 'xh2018'; 
```

立即生效:
```shell
mysql> flush privileges;
```

退出quit 或者 exit;
```shell
mysql> quit;
```
&nbsp;&nbsp;

### 开机服务启动设置：
把support-files/mysql.server 拷贝为/etc/init.d/mysql：
```shell
# cp -a /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

查看mysql服务是否在服务配置中
```shell
# chkconfig --list mysql
```

若没有，则把mysql注册为开机启动的服务，然后在进行查看
```shell
# chkconfig --add mysql
# chkconfig --list mysql
```

启动 或 停止
```shell
# service mysql start
# service mysql stop
```

服务启动后，直接运行mysql -u root -p即可登录，不需要进入到对应的目录。
```shell
# ln -s /usr/local/mysql/bin/mysql /usr/bin
```
