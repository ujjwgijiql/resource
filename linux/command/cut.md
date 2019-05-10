# cut

__1、 一两句话描述一下cut命令吧!__  
正如其名，cut的工作就是“剪”，具体的说就是在文件中负责剪切数据用的。  
cut是以每一行为一个处理对象的，这种机制和sed是一样的。  
&nbsp;&nbsp;

__2、 cut一般以什么为依据呢? 也就是说，我怎么告诉cut我想定位到的剪切内容呢?__  
cut命令主要是接受三个定位方法：  
* 第一，字节（bytes），用选项-b
* 第二，字符（characters），用选项-c
* 第三，域（fields），用选项-f  
&nbsp;&nbsp;

__3、 以“字节”定位，给个最简单的例子__  
举个例子吧，当你执行ps命令时，会输出类似如下的内容：
```shell
$ who
rocrocket :0           2009-01-08 11:07
rocrocket pts/0        2009-01-08 11:23 (:0.0)
rocrocket pts/1        2009-01-08 14:15 (:0.0)
```
如果我们想提取每一行的第3个字节，就这样：
```shell
$ who|cut -b 3
c
c
c
```
看明白了吧，-b后面可以设定要提取哪一个字节，其实-b和3之间没有空格也是可以的，但推荐有空格。  
&nbsp;&nbsp;

__4、 如果“字节”定位中，我想提取第3，第4、第5和第8个字节，怎么办?__  
-b支持形如3-5的写法，而且多个定位之间用逗号隔开就成了。看看例子吧：
```shell
$ who|cut -b 3-5,8
croe
croe
croe
```
但有一点要注意，cut命令如果使用了-b选项，那么执行此命令时，cut会先把-b后面所有的定位进行从小到大排序，然后再提取。  
可不能颠倒定位的顺序哦。这个例子就可以说明这个问题：
```shell
$ who|cut -b 8,3-5
croe
croe
croe
```
&nbsp;&nbsp;

__5、 还有哪些类似“3-5”这样的小技巧，列举一下吧!__
```shell
$ who
rocrocket :0           2009-01-08 11:07
rocrocket pts/0        2009-01-08 11:23 (:0.0)
rocrocket pts/1        2009-01-08 14:15 (:0.0)
$ who|cut -b -3
roc
roc
roc
$ who|cut -b 3-
crocket :0           2009-01-08 11:07
crocket pts/0        2009-01-08 11:23 (:0.0)
crocket pts/1        2009-01-08 14:15 (:0.0)
```
想必你也看到了，-3表示从第一个字节到第三个字节，而3-表示从第三个字节到行尾。  
如果你细心，你可以看到这两种情况下，都包括了第三个字节“c”。  
如果我执行who|cut -b -3,3-，你觉得会如何呢？  
答案是输出整行，不会出现连续两个重叠的c的。看：
```shell
$ who|cut -b -3,3-
rocrocket :0           2009-01-08 11:07
rocrocket pts/0        2009-01-08 11:23 (:0.0)
rocrocket pts/1        2009-01-08 14:15 (:0.0)
```
&nbsp;&nbsp;

__6、 给个以字符为定位标志的最简单的例子吧!__
下面例子你似曾相识，提取第3，第4，第5和第8个字符：
```shell
$ who|cut -c 3-5,8
croe
croe
croe
```
不过，看着怎么和-b没有什么区别啊？莫非-b和-c作用一样?  
其实不然，看似相同，只是因为这个例子举的不好，  
who输出的都是单字节字符，所以用-b和-c没有区别，  
如果你提取中文，区别就看出来了，来，看看中文提取的情况：
```shell
$ cat cut_ch.txt
星期一
星期二
星期三
星期四
$ cut -b 3 cut_ch.txt
�
�
�
�
]$ cut -c 3 cut_ch.txt
一
二
三
四
```
看到了吧，用-c则会以字符为单位，输出正常；而-b只会傻傻的以字节（8位二进制位）来计算，输出就是乱码。  
既然提到了这个知识点，就再补充一句，如果你学有余力，就提高一下。  
当遇到多字节字符时，可以使用-n选项，-n用于告诉cut不要将多字节字符拆开。    
例子如下：
```shell
$ cat cut_ch.txt |cut -b 2
�
�
�
�
$ cat cut_ch.txt |cut -nb 2

$ cat cut_ch.txt |cut -nb 1,2,3
星
星
星
星
```
&nbsp;&nbsp;

__7、 域是怎么回事呢？解释解释__
为什么会有“域”的提取呢，因为刚才提到的-b和-c只能在固定格式的文档中提取信息，  
而对于非固定格式的信息则束手无策。这时候“域”就派上用场了。  
（下面的讲解内容是在假设你对/etc/passwd文件的内容和组织形式比较了解的情况下进行的。）  
如果你观察过/etc/passwd文件，你会发现，它并不像who的输出信息那样具有固定格式，而是比较零散的排放。  
但是，冒号在这个文件的每一行中都起到了非常重要的作用，冒号用来隔开每一个项。  
我们很幸运，cut命令提供了这样的提取方式，具体的说就是设置“间隔符”，再设置“提取第几个域”，就OK了！  
以/etc/passwd的前五行内容为例：  
```shell
$ cat /etc/passwd|head -n 5
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
$ cat /etc/passwd|head -n 5|cut -d : -f 1
root
bin
daemon
adm
lp
```
看到了吧，用-d来设置间隔符为冒号，然后用-f来设置我要取的是第一个域，  
再按回车，所有的用户名就都列出来了！呵呵 有成就感吧！  
当然，在设定-f时，也可以使用例如3-5或者4-类似的格式：  
```shell
$ cat /etc/passwd|head -n 5|cut -d : -f 1,3-5
root:0:0:root
bin:1:1:bin
daemon:2:2:daemon
adm:3:4:adm
lp:4:7:lp
$ cat /etc/passwd|head -n 5|cut -d : -f 1,3-5,7
root:0:0:root:/bin/bash
bin:1:1:bin:/sbin/nologin
daemon:2:2:daemon:/sbin/nologin
adm:3:4:adm:/sbin/nologin
lp:4:7:lp:/sbin/nologin
$ cat /etc/passwd|head -n 5|cut -d : -f -2
root:x
bin:x
daemon:x
adm:x
lp:x
```
&nbsp;&nbsp;

__8、 如果遇到空格和制表符时，怎么分辨呢？我觉得有点乱，怎么办？__  
有时候制表符确实很难辨认，有一个方法可以看出一段空格到底是由若干个空格组成的还是由一个制表符组成的。
```shell
$ cat tab_space.txt
this is tab	finish.
this is several space      finish.
$ sed -n l tab_space.txt
this is tab\tfinish.$
this is several space      finish.$
```
看到了吧，如果是制表符（TAB），那么会显示为\t符号，如果是空格，就会原样显示。  
通过此方法即可以判断制表符和空格了。  
注意，上面sed -n后面的字符是L的小写字母哦，不要看错。  
&nbsp;&nbsp;

__9、 我应该在cut -d中用什么符号来设定制表符或空格呢?__  
悄悄的告诉你，cut的-d选项的默认间隔符就是制表符，所以当你就是要使用制表符的时候，  
完全就可以省略-d选项，而直接用－f来取域就可以了！放心，相信我！  
如果你设定一个空格为间隔符，那么就这样：  
```shell
$ cat tab_space.txt |cut -d ' ' -f 1
this
this
```
注意，两个单引号之间可确实要有一个空格哦，不能偷懒。  
而且，你只能在-d后面设置一个空格，可不许设置多个空格，因为cut只允许间隔符是一个字符。  
```shell
$ cat tab_space.txt |cut -d '  ' -f 1
cut: the delimiter must be a single character
Try `cut --help' for more information.
```
&nbsp;&nbsp;

__10、 我想将ps和cut命令配合使用时，怎么总是在最后两行出现重复现象？__
这个问题的具体描述是如下这样的。  
当cut和ps配合时：  
```shell
$ ps
  PID TTY          TIME CMD
 2977 pts/0    00:00:00 bash
 5032 pts/0    00:00:00 ps
$ ps|cut -b3
P
9
0
0
```
看，最后的0重复了两次！！而且，我也试过ps ef或ps aux均有此问题。  
而当ps和其他命令配合时，均无此问题，例如cut和who配合则正常：  
```shell
$ who
rocrocket :0           2009-01-08 11:07
rocrocket pts/0        2009-01-08 11:23 (:0.0)
rocrocket pts/1        2009-01-08 14:15 (:0.0)
$ who|cut -b3
c
c
c
```
其实这个问题是这样的，ps|cut会自身创建一个进程，所以当ps时也会提取出这个进程，  
然后通过管道输出到cut，所以cut截取后，就多出了一行，  
之所以会重复上一行内容，是由于我们恰巧取到了和上一行内容相同的字符而已。  
你测试下执行ps和ps|cat就知道原因了！  
&nbsp;&nbsp;

__11、 cut有哪些缺陷和不足？__
猜出来了吧？对，就是在处理多空格时。  
如果文件里面的某些域是由若干个空格来间隔的，那么用cut就有点麻烦了，因为cut只擅长处理“以一个字符间隔”的文本内容。


