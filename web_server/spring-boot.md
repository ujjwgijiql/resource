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
&nbsp;    
* MySQL的Bug导致
在Tomcat下无法清除MySQL连接线程，会导致服务挂掉，这是MySQL的bug，MySQL官方BUG信息：https://bugs.mysql.com/bug.php?id=69526。 可以通过建立监听器销毁线程的方式来解决，代码如下：
```java
/*
 * Copyright 2016-2017 TVI Go Easy.
 * Created on 2017/4/25 11:28
 */
package org.mechanic.fund.listener;

import com.mysql.jdbc.AbandonedConnectionCleanupThread;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Profile;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Enumeration;

/**
 * 清除数据库链接线程。该现象目前只发现在MySQL驱动上存在
 * MySQL官方BUG信息：https://bugs.mysql.com/bug.php?id=69526
 * 避免Tomcat reload application的是无法清除MySQL连接线程时抛出一下异常：
 * org.apache.catalina.loader.WebappClassLoader clearReferencesThreads
 * SEVERE: The web application [/XXX] appears to have started a thread named
 * [Abandoned connection cleanup thread] but has failed to stop it. This is very
 * likely to create a memory leak.
 *
 * [@author](https://my.oschina.net/arthor) mechanic
 * [@version](https://my.oschina.net/u/931210) 0.0.1
 */
@WebListener
@Profile({"test", "release"})
public class AbandonedConnectionCleanupTheardListener implements ServletContextListener {

    private static Logger logger = LoggerFactory.getLogger(AbandonedConnectionCleanupTheardListener.class);

    [@Override](https://my.oschina.net/u/1162528)
    public void contextInitialized(ServletContextEvent sce) {
        logger.info("AbandonedConnectionCleanupTheardListener is started.");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        Enumeration<Driver> drivers = DriverManager.getDrivers();

        Driver driver = null;

        // 清除驱动
        while (drivers.hasMoreElements()) {
            try {
                driver = drivers.nextElement();
                DriverManager.deregisterDriver(driver);
            } catch (SQLException ex) {
                logger.error("注销数据库连接线程失败。这种情况通常出现在MySQL上。");
            }
        }

        // MySQL driver leaves around a thread. This static method cleans it up.
        try {
            AbandonedConnectionCleanupThread.shutdown();
        } catch (InterruptedException e) {
            logger.error("AbandonedConnectionCleanupThread线程关闭失败。这种情况通常出现在MySQL上。");
        }
    }
}
```



