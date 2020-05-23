# 读取配置文件

## 说明

配置文件默认读取的是**application.properties**，如需读取其他的配置文件，需要使用`@PropertySource`注解。假设有一个配置文件**values.properties**在**resources**目录下。

<!--more-->

内容如下：

```yml
China.location=Asia
```

在需要调用值的类上添加`@PropertySource("classpath:values.properties")`

## 正文

新建`TestController.java`作为测试。

- #### 使用`@Value()`注解

  ```java
  @RestController
  @PropertySource("classpath:values.properties")
  public class TestController {
  
      @Value("${China.location}")
      private String location;
  
      @GetMapping("/")
      public String test() {
          return location;
      }
  }
  ```

  启动项目，访问[http://localhost:8080/](http://localhost:8080/)，出现“China”，说明读取数据成功。

- #### 使用`Environment`

  同一个类中，将代码修改如下：

  ```java
  @RestController
  @PropertySource("classpath:values.properties")
  public class TestController {
  
      private final Environment environment;
  
      @Value("${China.location}")
      private String location;
  
      @Autowired
      public TestController(Environment environment) {
          this.environment = environment;
      }
  
      @GetMapping("/")
      public String test() {
          System.out.println(location);
          System.out.println(environment.getProperty("China.location"));
          return "success";
      }
  }
  ```

  启动项目，访问[http://localhost:8080/](http://localhost:8080/)，出现“success”，查看控制台，出现

  ```
  Asia
  Asia
  ```

  说明读取数据成功。

- #### 使用`@ConfigurationProperties`注解

  如果有较多同类型内容，使用**properties**作为配置文件，看起来是不够清晰的，在**resources**目录下新建**values.yml**，内容如下：

  ```yaml
  person: 
    name: 张三
    age: 24
    gender: male
    nationality: China
    hobby: love
  ```

  变量较多时，使用上述所述的两种方法工作量会变得比较大，这时候我们通常使用基于类型安全的配置方式，将**properties**属性和一个**Bean**关联在一起，即使用注解`@ConfigurationProperties`读取配置文件数据。

  创建`Person.java`，内容如下：

  ```java
  @Component
  @ConfigurationProperties(prefix = "person")
  @PropertySource("classpath:values.yml")
  public class Person {
      private String name;
      private Integer age;
      private String gender;
      private String nationality;
      private String hobby;
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public Integer getAge() {
          return age;
      }
  
      public void setAge(Integer age) {
          this.age = age;
      }
  
      public String getGender() {
          return gender;
      }
  
      public void setGender(String gender) {
          this.gender = gender;
      }
  
      public String getNationality() {
          return nationality;
      }
  
      public void setNationality(String nationality) {
          this.nationality = nationality;
      }
  
      public String getHobby() {
          return hobby;
      }
  
      public void setHobby(String hobby) {
          this.hobby = hobby;
      }
  
      @Override
      public String toString() {
          return "Person{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  ", gender='" + gender + '\'' +
                  ", nationality='" + nationality + '\'' +
                  ", hobby='" + hobby + '\'' +
                  '}';
      }
  }
  ```

  这里也是可以使用**Lombok**插件的。

  若要使用`@ConfigurationProperties`，需要添加依赖。修改**pom.xml**，增加

  ```xml
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
      </dependency>
  ```

  继续修改`TestController.java`，注入**Person**

  ```java
  @RestController
  @PropertySource("classpath:values.properties")
  public class TestController {
  
      private final Person person;
  
      private final Environment environment;
  
      @Value("${China.location}")
      private String location;
  
      @Autowired
      public TestController(Person person, Environment environment) {
          this.person = person;
          this.environment = environment;
      }
  
      @GetMapping("/")
      public String test() {
          System.out.println(location);
          System.out.println(environment.getProperty("China.location"));
          System.out.println(person.toString());
          return "success";
      }
  }
  ```







------

使用`@Value` 注解`static`类型的变量，获取到的值是`null`

```java
@SpringBootApplication
@PropertySource("classpath:values.properties")
public class DemoApplication {

    @Value("${China.location}")
    private static String location;

    public static void main(String[] args) {

        SpringApplication.run(DemoApplication.class, args);

        System.out.println(location); // null

    }
}
```

应该这样写：使用非静态`set`方法获取`@Value()`的值

```java
@SpringBootApplication
@PropertySource("classpath:values.properties")
public class DemoApplication {

    private static String location;

    @Value("${China.location}")
    public void setLocation(String location) {
        DemoApplication.location = location;
    }

    public static void main(String[] args) {

        SpringApplication.run(DemoApplication.class, args);

        System.out.println(location); // Asia

    }
}
```



> 2020-3-12 
>
> 发现好像除了主类，其他类的静态变量通过上述方式得到的结果还是null。通过类上面添加`@Component`解决问题