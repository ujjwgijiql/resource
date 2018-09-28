# 遇到问题-----Springboot运行一段时间停止运行

### 原因
容器自动停止的坑有几种可能性，如果能够正常运行几天突然挂掉的话比较倾向是第3种情况。     
1、外部Tomcat和Spring boot内嵌的Tomcat有冲突    
2、MySQL的Bug导致    
3、还有一种可能性是在SSH远程启动Tomcat之后，退出SSH会同时关闭Tomcat    
&nbsp;    
### 解决方式
根据排查实验，我们的是第三种原因。     
回顾使用的启动语句    
```shell
java -jar biologic-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod >> catalina.out 2>&1 &
```    
项目启动后看起来似乎脱离终端了，其实还是受终端影响。大家引以为戒。     
只要终端关了 服务就会停止。     
把启动语句加上nohup后台运行就可以了。     
使用语句    
```shell
nohup java -jar biologic-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod >> catalina.out 2>&1 &
```
