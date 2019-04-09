# 玩转shutdown

### 1、我想立即关机！
```shell
$shutdown -h now
```  

### 2、我想立即重启
```shell
$shutdown -r now
```  

### 3、我想在23:30分准时关机
```shell
$shutdown -h 23:30
```  

### 4、我想在15分钟后关机
```shell
$shutdown -h +15
```  

### 5、总结一下，你可以看到，如果想让shutdown干活，你必须告诉shutdown命令一个时间，无论是now，还是23:30，还是+15。  
&nbsp;&nbsp;

### 6、我想来个恶作剧！  
恶作剧，就是吓唬用户。这个功能其实很实用，如果你是管理员，你完全可以利用“恶作剧”提醒所有登录的用户，让他们尽快退出程序，你要关机了。  
来看看怎么搞“恶作剧”：
```shell
$shutdown -k now “Server would shutdown in 15minutes\!”
```
看到了吧 加上-k选项就可以了 这样shutdown命令就知道这个命令只是一个恶作剧，不会真去关机的。  
shutdown会将你设定的消息字符串发给已登录的所有用户，这样就起到了提示的作用。试试-k吧！take it easy！
