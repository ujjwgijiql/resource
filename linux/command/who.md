今天要说的不是成龙的电影“我是谁”，而是Linux里的who系列命令，包括who、whoami和who am i。  
先看看这三个命令的输出信息：  
```shell
[rocrocket@rocrocket ~]$ whoami
rocrocket
[rocrocket@rocrocket ~]$ who am i
rocrocket pts/3        2008-12-30 13:17 (:0.0)
[rocrocket@rocrocket ~]$ who
rocrocket :0           2008-12-30 09:54
rocrocket pts/0        2008-12-30 09:55 (:0.0)
rocrocket pts/1        2008-12-30 09:57 (:0.0)
rocrocket pts/3        2008-12-30 13:17 (:0.0)
```
当我用sudo su（或者sudo su -）更换到root用户之后，你再看看：  
```shell
[rocrocket@rocrocket ~]$ sudo su
[root@rocrocket rocrocket]# whoami
root
[root@rocrocket rocrocket]# who am i
rocrocket pts/3        2008-12-30 13:17 (:0.0)
[root@rocrocket rocrocket]# who
rocrocket :0           2008-12-30 09:54
rocrocket pts/0        2008-12-30 09:55 (:0.0)
rocrocket pts/1        2008-12-30 09:57 (:0.0)
rocrocket pts/3        2008-12-30 13:17 (:0.0)
```
看出区别来了吧，whoami显示的是当前“操作用户”的用户名，而who am i显示的是“登录用户”的用户名。  
用Linux的术语来解释就是：（实际用户=uid，即user id。有效用户=euid，即effective user id）  

__who am i 显示的是实际用户的用户名，即用户登陆的时候的用户ID。此命令相当于who -m。  
whoami   显示的是有效用户ID.__  

好了，明白了两者区别之后，我们来说说who这个命令。  
有人会问，为什么我sudo su到root之后，who里面却没有显示呢？  
这是因为su过去的用户进程空间是作为一个子空间存在，他并没有得到一个登录的tty。  
who这个命令重点是用来查看当前有哪些用户登录到了本台机器上。  
who -m的作用和who am i的作用是一样的。  
who -q用来显示当前登录用户的个数。  
当你觉得who的输出信息晦涩难懂时，可以使用who -H来输出，这样可以在每列加上列名称，有助于阅读。  
