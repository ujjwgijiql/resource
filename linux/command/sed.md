# sed的流艺术

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
&nbsp;&nbsp;

__5、 sed中有选项么？有没有什么比较常用的？__  
sed最主要的就是command部分，把这部分玩转了，你就已经很厉害了，  
而玩转这部分的前提是玩转正则表达式。尽管如此，sed的选项部分的-n选项仍然是非常重要，  
理解了它，对于你提高sed水平也是至关重要。  
前面提到，sed会将模式空间里的行经过处理后输出到标准输出，这是默认的处理方式。  
就是说，除非你使用d命令来删除此行，否则它无论变成什么样子，无论它被替换成什么形状，  
它都会多多少少被输出到屏幕上。前面的例子可以证明这一切。你不信？给你个最能说明问题的例子：
```shell
# cat roc.txt
1
2
3
4
5
# sed ‘/4/p’ roc.txt
1
2
3
4
4
5
```
看，除了所有的原始内容都被输出来了，而且含有字符4的行被重复输出了一遍。  
这就是sed的工作原理，它会不问青红皂白的先把原始行输出来，然后再执行后面的动作，  
在这里我们设定了p，表示打印此行。这种输出结果不是我们想要的，我们其实想要的是只输出含有4的行。  
这时，如果你使用了-n选项，你会发现，结果如你所愿了：
```shell
# sed -n ‘/4/p’ roc.txt
4
```
选项-n告诉sed，除非是明确表明要输出的行，否则不要给我胡乱输出。（这回你知道-n的厉害了吧^_^）  
-n一般会和-p配合使用，意思是说输出那些发生变化的行。  
&nbsp;&nbsp;

__6、 command部分感觉很复杂，能给稍微总结一下么?__
ommand部分是这样，可以分为两部分，一部分是确定范围部分，一部分是处理方式部分。  
确定范围部分可以分为两种方式：  
* 指定行数：例如3,5表示第3、第4和第5行；5,$表示第5行至最后一行；  
* 用模式匹配进行指定：例如/^[^dD]/表示匹配行首不是以d或D开头的行等等。  
处理方式部分呢，有很多命令可用，介绍几个最常用的吧：  
* d:表示删除行  
* p:打印该行  
* r:读取指定文件的内容  
* w:写入指定文件  
* a\:在下面插入新行新内容  
&nbsp;&nbsp;

__7、 再举几个例子吧，给点经典的sed应用。__
例一 显示test文件的第10到20行：
```shell
# sed -n ‘10,20p’ test
```

例二 将所有以d或D开头的行的所有小写x变为大写X：  
```shell
# sed ‘/^[dD]/s/x/X/g’ test
```

例三 删除每行最后的两个字符：
```shell
# sed ‘s/..$//’ test
```
(有人会问用sed ‘/..$/d’ test为什么不行？ 这是因为/..$/表示匹配所有末尾含有两个字符的行，  
然后删除这样的行，显然这是错误的)

例四 删除每一行的前两个字符：
```shell
# sed ‘s/..//’ test
```

例五 给某些字符串后面插入些内容：
```shell
# cat mysed.txt
Beijing
# sed ‘s/B.*/&2008/’ mysed.txt
Beijing2008
London
```
这个命令的作用是将包含’B.*’的地方后面加上2008四个字符。这个命令里用到了&字符，  
这是一个技巧，高手和菜鸟比，这些技巧很重要哦。&表示“被匹配的部分”，在这里就是’Beijing’喽！  

例六 将行的第一个Beijing后加上2008，在最后一个Beijing后加上2008：
```shell
# cat mysed.txt
Beijing Beijing Beijing Beijing
London London London London
# sed ‘s/\(Beijing\)\(.*\)\(Beijing\)/\12008\2\32008/’ mysed.txt
Beijing2008 Beijing Beijing Beijing2008
London London London London
```
这个命令稍显复杂，其中用到了一个技巧，就是预存储，  
即被\(和\)括起来的匹配内容会被按顺序存储起来，存储到\1、\2…里面。  
这样你就可以使用\加数字来调用这些内容了。这个例子就是使用了这个技巧，  
分别存储了三个内容，分别为匹配Beijing、匹配.*和匹配Beijing。  
你可能会问，我如果想在第一个t前加上M，在第二个t前加上N，怎么做呢？  
这个就需要使用正则表达式的非贪婪算法了，一般是在?、+、*、{n}、{m,}、{m,n}的后面紧跟一个?，  
就会使匹配从贪婪算法变成非贪婪算 法，但是遗憾的是，sed并不支持这种非贪婪算法，  
所以我们无法使用sed实现这种想法，只能另寻其他工具了。这也算是sed的一个遗留问题吧。  

例七 可以用模式来定位行范围：
```shell
# cat mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
Beijing 2007
# sed -n ‘/2005/,/2007/p’ mysed.txt
Beijing 2005
Beijing 2006
Beijing 2007
```
我们使用/2005/来匹配行范围的首行，用/2008/来匹配行范围的最尾行。  
可以看到，在匹配尾行时，只要遇到符合要求的行，即停止，不会再继续向后匹配了。  
(只是匹配到第一个2007，并没有匹配到第二个2007)  

例八 用-e选项来设置多个command:
还记得command部分吧，现在我告诉你，sed命令可以包含不只一个的command，  
但是当包含超过一个的command的时候，必须在每个command前面加上选项-e。
```shell
# sed -n -e ‘1,2p’ -e ‘4p’ mysed.txt
Beijing 2003
Beijing 2004
Beijing 2006
```
虽然这个例子看似是为了使用-e而使用-e，但是-e在有些时候是很有用的。相信我，呵呵:)  
记住，-e后面必须立即接command，不允许再夹杂其他的选项了。  

例九 可以使用-f来设定包含command的文件名称:
如果你的command很长，那么可以将其写在文件里，然后使用-f来设定这个文件作为command部分：
```shell
# cat callsed
/2004/,/2006/p
# sed -n -f callsed mysed.txt
Beijing 2004
Beijing 2005
Beijing 2006
```
两个命令就讲清楚了-f的用法。相信你能看懂。

例十 如果设定了很多-e的command，它们的执行顺序是怎样的呢？:
```shell
# cat mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006Beijing 2007
Beijing 2008
# sed -e ‘s/Beijing/London/g’ -e ‘/Beijing/d’ mysed.txt
London 2003
London 2004
London 2005
London 2006
London 2007
London 2008
```
后一个-e中包含了删除Beijing所在行，但是最后的结果却是所有行都输出了，  
其实这就是由执行顺序导致的，第一个-e已经将Beijing都替换成了London了，  
所以怪不得第二个-e找不到Beijing了。  

例十一 读取一个特定文件的内容，将其插入到本文件指定的地方：
```shell
# cat ins.txt
====China====
[rocrocket@rocrocket programming]$ cat mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
# sed ‘/2005/r ins.txt’ mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
====China====
Beijing 2006
Beijing 2007
Beijing 2008
```
此命令用于在含有2005的行下面插入ins.txt文件的内容。  

例十二 将某些指定行写入到特定文件中:
```shell
# sed ‘/200[4-6]/w new.txt’ mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
# cat new.txt
Beijing 2004
Beijing 2005
Beijing 2006
```
看，w new.txt表示将含有2004、2005、2006的行写入到new.txt文件中。  

例十三 使用a\在特定行“下面”插入特定内容:
```shell
# cat new.txt
Beijing 2004
Beijing 2005
Beijing 2006
# sed ‘/2004/a\China’ mysed.txt
Beijing 2003
Beijing 2004
China
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
```
使用a\然后加上需要加的内容即可。   

例十四 使用i\在特定行“上面”插入特定内容:
```shell
# sed ‘/2004/i\China’ mysed.txt
Beijing 2003
China
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
```

例十五 替换特定字符:
```shell
# cat mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
# sed ‘y/eijng/EIJNG/’ mysed.txt
BEIJING 2003
BEIJING 2004
BEIJING 2005
BEIJING 2006
BEIJING 2007
BEIJING 2008
```
使用了y就表示将第一栏的每个字符都替换为相对应的第二栏的字符。  

例十六 对匹配行的下一行进行处理:
```shell
# cat mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
# sed ‘/2004/{n;y/eijng/EIJNG/;}’ mysed.txt
Beijing 2003
Beijing 2004
BEIJING 2005
Beijing 2006
Beijing 2007
Beijing 2008
```
这个例子就是找含有2004的行，然后将它下面的一行中的eijng替换为大写的EIJNG。  
这里面的“n;”起到了“移至下一行”的作用。n背后的含义其实是将下一行放到模式空间中去。  
```shell
# cat mysed.txt
Beijing 2003
Beijing 2004
Beijing 2005
Beijing 2006
Beijing 2007
Beijing 2008
# sed ‘/200/{n;y/eijng/EIJNG/;}’ mysed.txt
Beijing 2003
BEIJING 2004
Beijing 2005
BEIJING 2006
Beijing 2007
BEIJING 2008
```
这个例子更加典型，你可以发现，BEIJING是隔行出现的。你知道为什么么？不说答案了，相信你能自己思考出来！
