# 使用Druid作为项目数据源

Druid是一个JDBC组件库，包括数据库连接池、SQL Parser等组件。DruidDataSource是最好的数据库连接池。具体参见 [alibaba/druid](https://github.com/alibaba/druid/wiki/%E9%A6%96%E9%A1%B5)

<!--more-->

------

## 添加druid依赖

在pom.xml文件`<dependencies></dependencies>`中添加

```xml
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid-spring-boot-starter</artifactId>
      <version>1.1.10</version>
    </dependency>
```

此时的完整依赖组件应有

```xml
  <dependencies>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.2</version>
    </dependency>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid-spring-boot-starter</artifactId>
      <version>1.1.10</version>
    </dependency>
  </dependencies>
```

## 配置druid

此时修改application.yml，内容如下

```yaml
server:
  port: 7777

spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/springboot?useSSL=false
    username: root
    password: mysql
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      # 配置监控统计拦截的filters，去掉后监控界面sql将无法统计，'wall'用于防火墙
      filters: stat, wall
      # 最大活跃数
      max-active: 20
      # 初始化数量
      initial-size: 1
      # 最大连接等待超时时间
      max-wait: 60000
      # 通过connectionProperties属性来打开mergeSql功能；慢SQL记录
      connection-properties: druid.stat.mergeSql=true;ddruid.stat.slowSqlMillis=5000

      # 打开PSCache，并且指定每个连接PSCache的大小
      pool-prepared-statements: true
      maxPoolPreparedStatementPerConnectionSize: 20
      minIdle: 1
      timeBetweenEvictionRunsMillis: 60000
      minEvictableIdleTimeMillis: 300000
      validationQuery: select 1 from dual
      testWhileIdle: true
      testOnBorrow: false
      testOnReturn: false

  jpa:
    database: mysql
    show-sql: true
    # spring.jpa.properties.hibernate.hbm2ddl.auto是hibernate的配置属性，其主要作用是：自动创建、更新、验证数据库表结构。该参数的几种配置如下：
    # create：每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
    # create-drop：每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
    # update：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
    # validate：每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。
    properties:
      hibernate:
        hbm2ddl:
          auto: update

# Mybatis 配置
mybatis:
  typeAliasesPackage: com.luvnaxx.web.springboot.rookie.entity
  mapperLocations: classpath:mapper/*.xml
```

## 运行项目

此时，Spring Boot已经将druid作为dataSource加载到了项目中。在浏览器进行数据库的CRUD是没有问题的。

## 启动druid监控功能

在controller同级目录添加configuration包，新增`DruidConfiguration.java`，内容如下

```java
@Configuration
public class DruidConfiguration {

  @Bean
  public ServletRegistrationBean<StatViewServlet> statViewServlet() {
    // 创建servlet注册尸体
    ServletRegistrationBean<StatViewServlet> servletRegistrationBean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
    // 设置ip白名单
    servletRegistrationBean.addInitParameter("allow", "127.0.0.1");
    // 设置ip黑名单，如果allow与deny共同存在时，deny优先于allow
    // servletRegistrationBean.addInitParameter("deny", "xxx.xx.xxx.xxx");
    // 设置控制台管理用户
    servletRegistrationBean.addInitParameter("loginUsername", "druid");
    servletRegistrationBean.addInitParameter("loginPassword", "druid");
    // 是否可以重置数据
    servletRegistrationBean.addInitParameter("resetEnable", "false");
    return servletRegistrationBean;
  }

  @Bean
  public FilterRegistrationBean<WebStatFilter> statFilter() {
    // 创建过滤器
    FilterRegistrationBean<WebStatFilter> filterRegistrationBean = new FilterRegistrationBean<>(new WebStatFilter());
    // 设置过滤器过滤路径
    filterRegistrationBean.addUrlPatterns("/*");
    // 忽略过滤的形式
    filterRegistrationBean.addInitParameter("exclusions", "*.js,*.gif,*.jpg,*.png,*.ico,/druid/*");
    return filterRegistrationBean;
  }
}
```

再次启动项目，访问 [druid监控界面](localhost:7777/druid/login.html)，输入在代码中设置的账号密码即可进入druid控制界面。
