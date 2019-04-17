# man男人
大家在刚接触linux的时候，都会使用man来查阅一些命令的帮助信息。  
但是也都只是使用“man 命令名称”这样简单的格式。  
其实，man还有不少其他小技巧。

### 1、当你列出write时，它可能代表了Linux里的用户命令或系统调用，如果我只是想了解write这个系统调用的作用，而对write命令的命令格式没有兴趣，那么我该如何告诉man呢？
man命令后面可以加一个数字参数，表示具体的范围定位： 
* 数字“1”表示用户命令
* 数字“2”表示系统调用
* 数字“3”表示C语言库函数
* 数字“4”表示设备或特殊文件
* 数字“5”表示文件格式和规则
* 数字“6”表示游戏及其他
* 数字“7”表示宏、包及其他杂项
* 数字“8”表示系统管理员相关的命令  
知道了这些，我就可以用man 2 write来查看关于write系统调用的帮助信息了。  
再举几个例子： 
比如你man named，你观察屏幕左上角会发现NAMED(8)的字样，这就说明这个named是包含在“系统管理员相关的命令”之中的。  
再比如man yum.conf，你会发现yum.conf(5) ，我想你现在应该知道它的寓意了。  
下次你可以直接man 5再加一个配置文件，来查查这个文件的配置方法了。  
&nbsp;&nbsp;

### 2、我的这些man信息都存在Linux系统的哪里？
```shell
# manpath
/usr/kerberos/man:/usr/local/share/man:/usr/share/man/en:/usr/share/man
```
用manpath命令就可以看到了，当你man的时候，man会到如上这些路径去寻找对应的帮助信息。  
如果没有的话，那么man会抱怨：  
```shell
# man rocrocket
No manual entry for rocrocket
```

### 3、如何重新建立man的数据库？
使用makewhatis命令就可以！  
&nbsp;&nbsp;

### 5、既然有man数据库，我是否可以像搜索引擎那样搜出里面的关键字？
可以。这样做：  
```shell
# man -k roc
```
使用man的-k选项，就可以列出所有包含roc字符串的man帮助信息。  
其实，更深一步，man -k和apropos命令作用相同。你完全可以用apropos roc来代替man -k roc。  
其实apropos是一个脚本文件，它不是ELF可执行文件。所以你完全可以用vi /usr/bin/apropos看看它到底是如何实现都。



