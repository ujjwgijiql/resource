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
&nbsp;    
* 外部Tomcat和Spring boot内嵌的Tomcat有冲突
在外部Tomcat 8.5.12下部署Spring boot 1.4.3，系统运行了一段时间后tomcat会自动关闭，经研究发现是Srping内嵌的Tomcat在捣乱，具体的原因待查，解决办法倒是先蒙出来了：把内嵌tomcat去掉之后便正常了。    
由于开发环境需要内嵌Tomcat，而测试或生产环境实用的是外部Tomcat，因此如下直接将spring-boot-starter-web中去掉内嵌的tomcat会导致开发环境没有容器可用了：   
```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <exclusions>
        <exclusion>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
      </exclusion>
</dependency>
```    
在spring-boot-starter-web中tomcat依赖之后，独立增加tomcat依赖，注意这里有个自定义的${jarscope}需要在profiles中配置：
```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <scope>${jarscope}</scope>
</dependency>
```    
profiles配置中在开发环境增加<jarscope>compile</jarscope>，在测试和生产环境增加<jarscope>provided</jarscope>，这样就可在开发环境中保留内嵌tomcat，在生产和测试环境中去除内嵌tomcat：   
```xml
<profiles>
    <profile>
      <id>development</id>
      <properties>
        <environment>development</environment>
        <jarscope>compile</jarscope>
      </properties>
      <activation>
        <activeByDefault>true</activeByDefault>
      </activation>
    </profile>
    <profile>
      <id>test</id>
      <properties>
        <environment>test</environment>
        <jarscope>provided</jarscope>
      </properties>
    </profile>
    <profile>
      <id>release</id>
      <properties>
        <environment>release</environment>
        <jarscope>provided</jarscope>
      </properties>
    </profile>
  </profiles>
```    




