# bunzip2命令

并不是所有的linux压缩包都是.tar.gz的，你还会发现有一类文件的后缀是.tar.bz2的。此时，bunzip2命令就派上用场了。  
bunzip2和bzip2的关系。  
如果你在fedora下工作，你会发现bunzip2是bzip2的一个符 号连接。  
但bunzip2和bzip2的功能却正好相反。bzip2是用来压缩文件的（当然如果使用特殊函数也是可以用来解压，用选项-d可以实现），而 bunzip2是用来解压文件的（相当于bzip2 -d）。  
类比，还有zip和unzip、gzip和gunzip、compress和uncompress。  
&nbsp;&nbsp;

### 1、当你下载了一个文件为roc.tar.bz2时，该怎么解压它呢
```shell
# tar -xjvf roc.tar.bz2
```
j ：表示采用bunzip2工具。（yegle说可以省略j，即不特殊指定压缩工具，只用-xvf。这是也正确的^_^）  
&nbsp;&nbsp;

### 2、我有一个文件abc.txt，我想用bunzip2压缩工具进行压缩！
```shell
#bzip2 abc.txt
```
压缩后会得到一个压缩文件abc.txt.bz2，同时原abc.txt会被删除。（这点很重要哦，linux考试会问到这一点）  
&nbsp;&bnsp;

### 3、如果有一个文件abc.txt.bz2，想解压缩
```shell
#bunzip2 abc.txt.bz2
```
解压后会得到abc.txt，而原abc.txt.bz2会被删除。  
&nbsp;&nbsp;

### 4、如果我想压缩abc1.txt,abc2.txt,abc3.txt三个文件，还有dir1和dir2目录，共5个部分，一起压缩到five.bz2中
```shell
#bzip five.bz2 abc1.txt abc2.txt abc3.txt dir1 dir2
```

### 5、如果有abc1.txt.bz2和abc2.txt.bz2两个文件，我想用一个命令把两个文件分别解压为abc1.txt和abc2.txt
```shell
# bunzip2 abc1.txt.bz2 abc2.txt.bz2
```




