# gzip命令

### 1、下载了一个源码文件abc.tar.gz或abc.tgz（后缀tar.gz和tgz基本没啥区别，相同的还有.taz和.tar.Z）
```shell
# tar -xzvf abc.tar.gz
# tar -xzvf abc.tgz
```
tar.gz和tgz是经过归档并由gzip工具压缩之后所得到的压缩包。  
x ：表示解压缩  
z ：表示用gzip工具进行解压缩  
v ：表示在解压缩时显示详细信息  
f ：表示指定文件（请注意，这个选项一定要放在各个选项的最后哦～～，也就是要和所指定的文件名挨得最近哦）  
&nbsp;&nbsp;

### 2、遇到一个文件xyz.gz，想解压缩
```shell
# gzip -d xyz.gz
```
d ：选项表示解压缩  
&nbsp;&bnsp;

### 3、遇到一个文件edf.tar，想解压缩：（严格的讲tar文件是归档文件，并未被压缩，这里提到的“解压缩”只是将tar文件拆开而已）
```shell
# tar -xvf edf.tar
```
其实这个命令不是压缩命令范畴，只是在这里提一下  
&nbsp;&nbsp;

### 4、想将一个文件夹dirabc压缩成.tar.gz的压缩文件
```shell
# tar -czvf dirabc.tar.gz dirabc
```

### 5、想查看一下下载的abc.tar.gz压缩文件里包含哪些文件
```shell
# tar -ztvf abc.tar.gz
```

### 6、用5的方法查看到abc.tar.gz压缩包，其中包括def/xyz.txt文件等很多文件，但只想提取出xyz.txt这一个文件
```shell
＃ tar -xzvf abc.tar.gz def/xyz.txt
```
