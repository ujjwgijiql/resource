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
