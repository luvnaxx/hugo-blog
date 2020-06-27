# 使用Log4j2作为日志组件

Spring Boot默认使用LogBack作为日志工具，修改为Log4j2。

<!--more-->

------

## 引入依赖

```xml
    <!-- 移除原有的logBack -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
      <exclusions>
        <exclusion>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <!-- 添加log4j2的依赖 -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
```

## 配置Log4j2

在**resources**下新建`log4j2.xml`，内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!-- status log4j2内部日志级别 -->
<configuration status="INFO">
  <!-- 全局参数 -->
  <Properties>
    <Property name="pattern">%d{yyyy-MM-dd HH:mm:ss,SSS} %-5p %c{1}:%L -%m%n</Property>
    <Property name="displayName">rookie</Property>
  </Properties>
  <Appenders>
    <Console name="console" target="SYSTEM_OUT" follow="true">
      <PatternLayout>
        <pattern>${pattern}</pattern>
      </PatternLayout>
    </Console>
    <!-- 文件 每次运行程序会自动清空，由append属性决定 -->
    <File name="error" fileName="${displayName}_error.log" append="false">
      <!-- 指定error 级别的日志 -->
      <ThresholdFilter level="ERROR" onMatch="ACCEPT"
                       onMismatch="DENY"/>
      <PatternLayout>
        <pattern>${pattern}</pattern>
      </PatternLayout>
    </File>
    <!-- 滚动文件 -->
    <RollingFile name="rollingFile" fileName="${displayName}.log"
                 filePattern="${displayName}_%d{yyyy-MM-dd}.log">
      <PatternLayout>
        <pattern>${pattern}</pattern>
      </PatternLayout>
      <!-- 按大小划分 -->
      <SizeBasedTriggeringPolicy size="50 MB"/>
    </RollingFile>
  </Appenders>
  <Loggers>
    <Logger name="com.luvnaxx.web.springboot.rookie.controller" level="WARN"/>
    <Logger name="com.luvnaxx.web.springboot.rookie" level="INFO"/>
    <Root level="INFO">
      <AppenderRef ref="console"/>
      <AppenderRef ref="error"/>
      <AppenderRef ref="rollingFile"/>
    </Root>
  </Loggers>
</configuration>
```