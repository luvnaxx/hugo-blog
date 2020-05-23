# ConditionOnProperty注解的使用

> **Spring Boot**通过**@ConditionalOnProperty**来控制**Configuration**是否生效

<!--more-->

------

#### Service

**DemoService.java**

```java
public interface DemoService {

  String getName(String name);
}
```

**DemoServiceImpl1.java**

```
@Service
@ConditionalOnProperty(name = "configuration.read", havingValue = "one")
public class DemoServiceImpl1 implements DemoService {

  @Override
  public String getName(String name) {
    return name + "实现1";
  }
}
```

**DemoServiceImpl2.java**

```
@Service
@ConditionalOnProperty(name = "configuration.read", havingValue = "two")
public class DemoServiceImpl2 implements DemoService {

  @Override
  public String getName(String name) {
    return name + "实现2";
  }
}
```

#### 配置文件

**application.properties**

```properties
configuration.read=one #two
```

#### Controller

**HelloController.java**

```java
@RestController
public class HelloController {

    @Autowired
    private DemoService demoService;

    @GetMapping("/test")
    public String demo(@RequestParam String name) {
        return demoService.getName(name);
    }
}
```



通过配置属性为one/two来使用不同的实现