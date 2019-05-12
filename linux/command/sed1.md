# sed的流艺术之一

__1、 你想过sed为什么叫sed么？__
sed是stream editor的缩写，sed就是一个流编辑器，  
说白了，就是我们往sed里输入一串信息，它给我们处理，然后输出来。就这么简单。  
&nbsp;&nbsp;
sed和cut类似，是一个面向行处理的工具，它以“行”为处理单位，处理后的结果会输出到标准输出。  
所以说其实sed命令很安全的，它不会对行本身所在文件做任何修改的。  
&nbsp;&nbsp;
sed的命令格式是：sed command file  
&nbsp;&nbsp;
其中，command部分是针对每行数据所做的处理（这部分很重要很重要，接着看，你会知道的），  
file是要处理的文件，如果忽略file参数，则sed会把标准输入作为处理对象。  
&nbsp;&nbsp;

__2、 sed的工作原理是什么?__
sed会一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），  
接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。  
接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。  
&nbsp;&nbsp;

__3、 给个最最简单的sed命令的例子吧！让我先有个感性认识，呵呵。__
```shell
# cat roc.txt
test 1
test2
testtest
XtestX
BBtest
# sed ‘/2/d’ roc.txt
test 1
testtest
XtestX
BBtest
```
此例子是利用sed来删除roc.txt文件里含有字符“2”的行。  
大家看到了，例子很简单，这个命令的command部分是/2/d，而且它是用单引号括起来的。  
你也一定要学着这样做，只要用到sed，别忘了用单引号将command部分括起来。
2/d中的d表示删除，意思就是说只要某行含有字符2，则删除之。  
（你一定没忘记，sed所谓的删除都是在模式空间中执行的，而原文件roc.txt毫发无损）  
&nbsp;&nbsp;

__4、 想实现类似于cut -d : -f 1 /etc/passwd的效果，用sed怎么做呢？__
```shell
# head -n 5 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin

# head -n 5 /etc/passwd|sed ‘s/:.*$//’
root
bin
daemon
adm
lp
```
看到了吧，我们使用的command是’s/:.*$//’，其中的正则表达式s///是替换的经典格式.

