# 使用Swagger2构建RESTful API

给项目的接口添加接口说明是一个很好的习惯。对于越来越大，越来越多的接口，维护说明是一个比较麻烦的工作；如果接口内容发生变化，还要及时更新文档，不然会造成说明与实际不符的情况。所以我们需要一个能配合程序产生API文档的小伙伴。Swagger2就是一款RESTful接口的文档在线自动生成+功能测试功能软件。

<!--more-->

------

## 添加依赖

在`pom.xml`中添加如下依赖

```xml
    <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.8.0</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.8.0</version>
    </dependency>
```

## 编写代码

#### 创建Swagger2配置类

在`configuration`包下新建`Swagger2Configuration.java`。内容如下

```java
@Configuration
@EnableSwagger2
public class Swagger2Configuration {

  @Bean
  public Docket createRestApi() {
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.luvnaxx.web.springboot.rookie"))
        .paths(PathSelectors.any())
        .build();
  }

  private ApiInfo apiInfo() {
    Contact contact = new Contact("luvnaxx", "https://www.luvnaxx.com", "luvnaxx@gmail.com");
    return new ApiInfoBuilder()
        .title("Spring Boot中使用Swagger2构建RESTful APIs")
        .description("学习Spring Boot的简单小项目")
        .contact(contact)
        .version("1.0")
        .build();
  }
}
```

通过`@Configuration`注解，让Spring来加载该类配置。再通过`@EnableSwagger2`注解来启用Swagger2

#### 添加文档内容

在我们需要的方法上添加注解内容来丰富文档的内容。仅仅以一个方法作为示例：在`UserRestController.java`中添加如下内容

```java
  /**
   * 返回指定id用户
   *
   * @param id 用户id
   */
  @GetMapping("{id}")
  @ApiOperation(value = "获取指定用户", notes = "根据id返回指定用户")
  @ApiImplicitParam(name = "id", value = "用户id", required = true, dataType = "Long", paramType = "path")
  public Optional<User> findById(@PathVariable("id") Long id) {
    return userService.findById(id);
  }
```

通过`@ApiOperation`注解来给API增加说明、通过`@ApiImplicitParams`、`@ApiImplicitParam`注解来给参数增加说明。

## 运行测试

启动项目，访问 [<http://localhost:7777/swagger-ui.html>](http://localhost:7777/swagger-ui.html) 