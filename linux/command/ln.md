# ln命令

ln是一个很神奇的命令，它可以创建一个文件的影子，也可以通过一个通道进入另一个地方。  
:)其实，所有的这些把戏都是通过软连接和硬连接实现的。  
&nbsp;&nbsp;  

__1、 我想建一个source.txt文件的硬链接，名字取做linksource.txt__
```shell
$ cat source.txt
Hello!Source!
$ ll source.txt
-rw-rw-r– 1 rocrocket rocrocket 14 2008-10-23 09:25 source.txt
$ ln source.txt linksource.txt
$ ll -i source.txt linksource.txt
1178119 -rw-rw-r– 2 rocrocket rocrocket 14 2008-10-23 09:25 linksource.txt
1178119 -rw-rw-r– 2 rocrocket rocrocket 14 2008-10-23 09:25 source.txt
```
可以看到，我们建立硬链接的命令格式是“ln 目标 链接名称”，  
我们用ln source.txt linksource.txt来建立了一个source.txt文件的硬链接。  
之后，我们用ll -i，即ls -li命令来查看了文件的信息，  
其中-i选项表示列出每个文件的inode节点ID，  
我们可以发现source.txt和linksource.txt的inode号是完全一样的，都是1178119，  
这就说明它们都指向同一个数据块。  
这就是硬链接。  

但要注意，硬链接不允许跨分区来建立，也就是不允许跨文件系统来建立，  
即使是同一类型的文件系统也是不行的。所以硬链接只能在一个分区内建立。  
&nbsp;&nbsp;  

__2、 我想建立一个source.txt文件的软链接，名字叫做softsource。__
```shell
$ ln -s source.txt softsource
$ ll -i source.txt softsource
1178211 lrwxrwxrwx 1 rocrocket rocrocket 10 2008-10-23 09:32 softsource -> source.txt
1178119 -rw-rw-r– 2 rocrocket rocrocket 14 2008-10-23 09:25 source.txt
```
可以看到，建立软链接也是使用ln命令，但是必须加上-s选项，建立软链接的命令格式为“ln -s 目标 链接名称”。  
之后我们可以使用ls命令可以查看到软链接softsource和source.txt的inode号是不一样的，  
这说明他们完全指向两个不同的数据库快。  
而且，细心的朋友能够观察到软链接文件的权限栏首字符为l，这也是软链区别于普通文件的地方之一。  
&nbsp;&nbsp;  

如果这个时候，我们删除了source.txt文件，则软链接softsource就会编程红色字体，  
这表示警告，这是一个有问题的文件，它无法找到它所标识的文件source.txt了。  
&nbsp;&nbsp;  

__3、 刚才的两个例子都是链接文件的，那链接目录呢，我想试试！__
```shell
$ ln tempdir linkdir
ln: `temp’: hard link not allowed for directory
```
我想硬链接一个目录tempdir，但是报错了！是的，硬链接是不允许链接到目录的。（待会儿来分析原因，少安毋躁）  

```shell
$ ln -s temp linkdir
```
目录的软链接的建立是允许的，看，我建立了一个temp目录的软链接linkdir，  
以后我完全可以cd linkdir来进入temp目录了。像这样：
```shell
$ cd linkdir/
$ ls
a001.txt  a002.c  a.out  sixunhuan.c
$ cd ../temp/
$ ls
a001.txt  a002.c  a.out  sixunhuan.c
$
```
我们来查看下这两个目录的属性信息：
```shell
$ ll|grep -E “temp|linkdir”
lrwxrwxrwx 1 rocrocket rocrocket     4 2008-10-23 09:49 linkdir -> temp
drwxr-xr-x 2 rocrocket rocrocket  4096 2008-08-07 16:08 temp
```
可以看到linkdir是软链接，而temp是一个目录。  
&nbsp;&nbsp;

__4、 为什么ln不允许硬链接到目录，而允许软链接到目录呢？__
linux系统中的硬连接有两个限制：不能跨越文件系统和不允许普通用户对目录作硬连接。至于第一个限制，很好理解，而第二个就不那么好理解了。 我们对任何一个目录用ls -l 命令都可以看到其连接数至少是2，这也说明了系统中是存在硬连接的，而且命令ln -d 也可以让超级用户对目录作硬连接，这些都说明了系统限制对目录进行硬连接只是一个硬性规定，并不是逻辑上不允许或技术上的不可行。那么操作系统为什么要进行限制呢？
如果引入了对目录的硬连接就有可能在目录中引入循环，那么在目录遍历的时候系统就会陷入无限循环当中。也许您会说，符号连接不也可以引入循环吗，那么为什么不限制目录的符号连接呢？原因就在于在linux系统中，每个文件(目录也是文件)都对应着一个inode结构，其中inode数据结构中包含了文件类型(目录，普通文件，符号连接文件等等)的信息，也就是说操作系统在遍历目录时可以判断出符号连接，既然可以判断出符号连接当然就可以采取一些措施来防范进入过大的循环了，系统在连续遇到8个符号连接后就停止遍历，这就是为什么对目录符号连接不会进入死循环的原因了。但是对于硬连接，由于操作系统中采用的数据结构和算法限制，目前是不能防范这种死循环的。”  
&nbsp;&nbsp;

__5、 ln命令里面有一个-n选项，它的官方解释是这样的：-n, –no-dereference  treat destination that is a symlink to a directory as if it were a normal file. 请问这是什么意思阿？__
第一步：mkdir a b  
第二步：ln -sf a c (这个时候c -> a)  
第三步：如果执行ln -sf b c，那么会在c目录“里面”创建一个b的symbolic link，但很明显这不是你的本意。  
如果把第三步替换成：ln -snf b c 那么结果就变成了c->b

