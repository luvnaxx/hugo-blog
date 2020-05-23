## 加入 Zipkin Server

pom.xml

<!--more-->

```xml
    <!-- https://mvnrepository.com/artifact/io.zipkin.java/zipkin-server -->
    <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-server</artifactId>
      <version>2.12.3</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/io.zipkin.java/zipkin-autoconfigure-ui -->
    <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-autoconfigure-ui</artifactId>
      <version>2.12.3</version>
    </dependency>
```



## 异常处理

#### 在 Spring Boot 启动类加上 `@EnableZipkinServer` 注解，启动项目，控制台报错

> SLF4J: Class path contains multiple SLF4J bindings.
> SLF4J: Found binding in [jar:file:/C:/repository/org/apache/logging/log4j/log4j-slf4j-impl/2.11.2/log4j-slf4j-impl-2.11.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
> SLF4J: Found binding in [jar:file:/C:/repository/ch/qos/logback/logback-classic/1.2.3/logback-classic-1.2.3.jar!/org/slf4j/impl/StaticLoggerBinder.class]
> SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
> SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
> Exception in thread "main" java.lang.StackOverflowError

**需要排除 zipkin-server 中依赖的 SLF4j**

```xml
    <dependency>
      <groupId>io.zipkin.java</groupId>
      <artifactId>zipkin-server</artifactId>
      <version>2.12.3</version>
      <exclusions>
        <exclusion>
          <artifactId>spring-boot-starter-log4j2</artifactId>
          <groupId>org.springframework.boot</groupId>
        </exclusion>
      </exclusions>
    </dependency>
```

#### 默认端口9411，当使用其他端口，启动web页面，出现

> java.lang.IllegalArgumentException: Prometheus requires that all meters with the same name have the same set of tag keys. There is already an existing meter named 'http_server_requests_seconds' containing tag keys [exception, method, outcome, status, uri]. The meter you are attempting to register has keys [method, status, uri].

在配置文件中添加

```yaml
management:
  metrics:
    web:
      server:
        auto-time-requests: false
```

