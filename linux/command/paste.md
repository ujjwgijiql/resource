# paste

paste命令，是一个专门和cut对着干的命令，作用是将几个文件拼凑在一起。  
&nbsp;&nbsp;

__1、 paste的拼凑原理是什么?__  
这个很简单，和cut的原理几乎一样，就是将几个文件的相应行用制表符连接起来，并输出到标准输出。  
paste最简单的使用方法是： 
```shell
$ cat p1.txt
1
2
3
$ cat p2.txt
a
b
c
$ paste p1.txt p2.txt
1    a
2    b
3    c
$ paste p1.txt p2.txt|sed -n l
1\ta$
2\tb$
3\tc$
```
看到制表符了吧，它就是默认的间隔符。  
&nbsp;&nbsp;

__2、 上面的例子是两个文件的，那三个文件呢？__  
当然也可以了。看看例子吧：  
```shell
$ cat p3.txt
I
II
III
$ paste p1.txt p2.txt p3.txt
1    a    I
2    b    II
3    c    III
$ paste p3.txt p2.txt p1.txt
I    a    1
II   b    2
III  c    3
```
可见paste的结果是和文件列表的顺序有关的。
&nbsp;&nbsp;

__3、 我不想用制表符来间隔，我想用星号(*)，可以么？__
行的！使用-d符号就可以设定间隔符，这和cut没有两样。看看例子：
```shell
$ paste -d '*' p3.txt p2.txt p1.txt
I*a*1
II*b*2
III*c*3
```
要注意一点，此处一定要把星号用引号括起来哦（单引号双引号均可），  
否则shell会把星号扩展为当前目录下的文件列表哦，小心小心。
&nbsp;&nbsp;

__4、 我看过paste命令的man，里面提到了一个-s选项，我不懂是什么意思？__
这个很好解释，继续上面的例子，看看这个命令：
```shell
$ paste -s -d "*" p3.txt p2.txt p1.txt
I*II*III
a*b*c
1*2*3
```
呵呵，懂了吧，-s就是以每个文件为一个处理单元，将其中的所有行以-d设定的间隔符相连接组成一大行，输出到标准输出中。
