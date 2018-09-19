# SpringBoot中logback使用application.yml中属性
logback的日志文件命名是<span style="color:#FF000000;">__`logback-spring.xml`__</span>，而不是logback.xml，原因是，命名为logback-spring.xml的日志配置文件，spring boot可以为它添加一些spring boot特有的配置项。日志文件名不用logback-spring.xml，会引用不到application.yml中的属性。    
* springProfile标签    
springProfile标签可以配置日志文件的任何标签位置，然后可以在application.properties配置文件中，通过spring.profiles来切换profile标签    
```xml
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev, staging">
    <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
    <!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```    
* springProperty标签    
  * 该`<springProperty>`标签允许我们从Spring中显示属性，Environment 以便在Logback中使用。如果你想引用 application.properties中配置的属性，这将非常有用    
  * 标签的工作方式与Logback的标准 <property> 标签类似，但不是直接value 指定source属性（从Environment）指定。scope 如果需要将属性存储在local范围之外的其他位置，则可以使用该属性。如果您需要一个后备值，以防该属性未设置，则Environment可以使用该defaultValue属性。    
&nbsp;    

application.properties
```properties
# 日志配置
logging.config=classpath:logback-spring.xml
logging.path=/opt/logs/spring-boot
log.max.history=30
log.immediate.flush=true

# DEBUG INFO WARN ERROR
log.leveling=INFO
```    
logback-spring.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/2002/xmlspec/dtd/2.10/xmlspec.dtd">

<configuration>

    <springProperty scope="context" name="log.dir" source="logging.path" defaultValue="/opt/logs/spring-boot"/>
    <springProperty scope="context" name="log.leveling" source="log.leveling" defaultValue="WARN"/>
    <springProperty scope="context" name="log.max.history" source="log.max.history" defaultValue="7"/>
    <springProperty scope="context" name="log.immediate.flush" source="log.immediate.flush" defaultValue="true"/>


    <!-- 文件输出格式 -->
    <!-- <property name="PATTERN" value="%date{yyyy-MM-dd HH:mm:ss} | %highlight(%-5level) | %boldYellow(%thread) | %boldGreen(%logger) | %msg | [%file:%line]%n "/> -->
    <property name="PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level %marker %logger{32}.%M:%L %msg%n"/>
    <property name="PATTERN_ALERT" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level %logger{32}.%M:%L %msg%n%xException%n"/>


    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>DEBUG</level>
        </filter>
        <immediateFlush>false</immediateFlush>
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>${PATTERN}</pattern>
        </layout>
    </appender>


    <appender name="FILE_ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/css/logs_error.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/css/bak/logs_error.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${PATTERN}</pattern>
            <immediateFlush>${log.immediate.flush}</immediateFlush>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <appender name="FILE_WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/css/logs_warn.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/css/bak/logs_warn.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${PATTERN}</pattern>
            <immediateFlush>${log.immediate.flush}</immediateFlush>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <appender name="FILE_INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/css/logs_info.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/css/bak/logs_info.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${PATTERN}</pattern>
            <immediateFlush>${log.immediate.flush}</immediateFlush>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <appender name="FILE_DEBUG" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/css/logs_debug.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/css/bak/logs_debug.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${PATTERN}</pattern>
            <immediateFlush>${log.immediate.flush}</immediateFlush>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>DEBUG</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>


    <appender name="BIZ_ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/css/biz_error.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/css/bak/biz_error.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>utf-8</charset>
            <ImmediateFlush>${log.immediate.flush}</ImmediateFlush>
            <pattern>${PATTERN}</pattern>
        </encoder>
    </appender>


    <!-- 这个日志是给kibana采集用的 -->
    <appender name="ALERT_MONITOR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/alert_monitor.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/bak/alert_monitor.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>utf-8</charset>
            <ImmediateFlush>${log.immediate.flush}</ImmediateFlush>
            <pattern>${PATTERN_ALERT}</pattern>
        </encoder>
    </appender>


    <appender name="LONGTIME" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log.dir}/css/time_long.log</file>
        <Append>true</Append>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.dir}/css/bak/time_long.%d{yyyy-MM-dd}.log.zip</fileNamePattern>
            <maxHistory>${log.max.history}</maxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <charset>utf-8</charset>
            <ImmediateFlush>${log.immediate.flush}</ImmediateFlush>
            <pattern>${PATTERN}</pattern>
        </encoder>
    </appender>



    <root level="${log.leveling}">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="FILE_ERROR"/>
        <appender-ref ref="FILE_WARN"/>
        <appender-ref ref="FILE_INFO"/>
        <appender-ref ref="FILE_DEBUG"/>
    </root>


    <!-- FILE_WARN FILE_ERROR -> ALERT_MONITOR  -->
    <!-- error -->
    <logger name="FILE_ERROR" level="${log.leveling}" additivity="false">
        <appender-ref ref="ALERT_MONITOR"/>
    </logger>

    <!-- warn -->
    <logger name="FILE_WARN" level="${log.leveling}" additivity="false">
        <appender-ref ref="ALERT_MONITOR"/>
    </logger>

    <!-- biz_error -->
    <logger name="BIZ_ERROR" level="${log.leveling}" additivity="false">
        <appender-ref ref="ALERT_MONITOR"/>
        <appender-ref ref="BIZ_ERROR"/>
    </logger>

    <!-- longtime -->
    <logger name="LONGTIME" level="${log.leveling}" additivity="false">
        <appender-ref ref="ALERT_MONITOR"/>
        <appender-ref ref="LONGTIME"/>
    </logger>


    <logger name="java.sql.ResultSet" level="${log.level}" />
    <logger name="org.apache.ibatis" level="${log.level}" />
    <logger name="java.sql.PreparedStatement" level="${log.level}" />
    <logger name="java.sql.Connection" level="${log.level}" />
    <logger name="org.mybatis.spring" level="${log.level}" />

</configuration>
```    
__将RelaxedPropertyResolver用于访问环境属性。如果使用虚线符号指定source（my-property-name）所有的变化都会被尝试（myPropertyName，MY_PROPERTY_NAME等）。__   




