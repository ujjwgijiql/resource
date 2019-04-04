# service
### 1. 声明：这个命令不是在所有的linux发行版本中都有。主要是在redhat、fedora、mandriva和centos中。
### 2. 此命令位于/sbin目录下，用file命令查看此命令会发现它是一个脚本命令。
### 3. 分析脚本可知此命令的作用是去/etc/init.d目录下寻找相应的服务，进行开启和关闭等操作。
### 4. 开启httpd服务器：service httpd start
start可以换成restart表示重新启动，stop表示关闭，reload表示重新载入配置。
### 5. 关闭mysql服务器：service mysqld stop
### 6. 强烈建议大家将service命令替换为/etc/init.d/mysqld stop
