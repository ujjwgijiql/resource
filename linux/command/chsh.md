# 用chsh选择shell

### 1、我想知道我机器安装了哪些shell？
两种方法可以查看：  
第一种：  
```shell
# chsh -l
/bin/sh
/bin/bash
/sbin/nologin
/bin/zsh
```
第二种：
```shell
# cat /etc/shells
/bin/sh
/bin/bash
/sbin/nologin
/bin/zsh
```
其实chsh -l也是来查看这个文件。
&nbsp;&nbsp;

### 2、我想知道我当前正在使用的shell是哪个阿？
```shell
# echo $SHELL
/bin/bash
```
注意SHELL一定要是大写。可以看到，我目前使用的shell是/bin/bash
&nbsp;&nbsp;

### 3、执行了zsh之后，我查看当前shell类型仍然是/bin/bash呢？
请注意，我们虽然执行了zsh，但是所谓“当前的shell”是一个大环境的概念，是针对一个已登录的用户而言的。  
而我们执行zsh只是启动了一个zsh的解释器程序而已，并没有改变大环境。  
如果想改变“当前的shell”，那么还是要使用chsh才可以。
&nbsp;&nbsp;

### 4、我想把我的shell改成zsh！
```shell
# chsh -s /bin/zsh
Changing shell for rocrocket.
Password:
Shell changed.
```
使用chsh加选项-s就可以修改登录的shell了！  
你会发现你现在执行echo $SHELL后仍然输出为/bin/bash，这是因为你需要重启你的shell才完全投入到zsh怀抱中去。  
&nbsp;&nbsp;

### 5、chsh -s到底是修改了哪里？
chsh -s其实修改的就是/etc/passwd文件里和你的用户名相对应的那一行。现在我来查看下：
```shell
# cat /etc/passwd|grep ^rocrocket
rocrocket:x:500:500:rocrocket,China:/rocrocket/PSB/home:/bin/zsh
```
你可以发现输出内容的最后部分已经变成了/bin/zsh了！  
下次你重启的时候，linux就会读取这一命令来启动你的shell了！  
好了，我要恢复正常工作，把shell修改会我熟悉的/bin/bash了！  
```shell
# chsh -s /bin/bash
Changing shell for rocrocket.
Password:
Shell changed.
```
