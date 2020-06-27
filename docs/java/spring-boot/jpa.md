# 使用Druid作为项目数据源

## 数据库准备

新建数据库springboot，数据库表tb_user，添加id、username、password、age、address字段。

```mysql
CREATE TABLE tb_user
(
  id int PRIMARY KEY NOT NULL AUTO_INCREMENT,
  username varchar(20) NOT NULL,
  password varchar(50) NOT NULL,
  age int,
  address varchar(100)
);
ALTER TABLE tb_user COMMENT = '用户信息表';
```

*关于数据库的命名规范，虽说没有一个强制的规定说数据库要如何命名、数据表要如何命名、字段值要如何命名，但是似乎很多都是有一个潜规则。我又是一个有些强迫症的人，很多时候我为了一个命名，可以想个半天，有时甚至删项目重搞。可怕*
*对于MySQL数据库的各种命名，在我翻过各种高山，拜访过各位民间大神之后，现在的习惯是数据库名和表名采用 "_"连接的形式，据说这是为了方面在Windows与类Unix机器上的兼容。而对于数据表中的字段，我现在使用Java变量名命名的规范，即驼峰原则，这样可能比较有利于与Java的配合。*

## 新建Spring Boot项目

进入[SPRING INITIALIZR][1] 或者使用JetBrains IDEA/Eclipse构建一个Spring Boot项目(这里使用2.0.3.RELEASE版本)。选择添加Web、JPA、MySQL依赖，或者稍后手动在pom.xml中添加。
最终的pom.xml文件中应该包含有如下依赖：

```xml
  <dependencies>

    <dependency>

      <groupId>org.springframework.boot</groupId>

      <artifactId>spring-boot-starter-data-jpa</artifactId>

    </dependency>

    <dependency>

      <groupId>org.springframework.boot</groupId>

      <artifactId>spring-boot-starter-web</artifactId>

    </dependency>



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

      <artifactId>spring-boot-starter-test</artifactId>

      <scope>test</scope>

    </dependency>

  </dependencies>
```

*可以看到，这里多添加了一个lombok依赖。lombok是一个简化Java代码的工具，通过添加注解，在实体类中就可以不再手动编写getter/setter方法，虽说现在各种IDE已经可以自动帮助我们生成getter/setter方法，但是简化文件的内容还是很不错的。*



## 创建配置文件

在resources文件夹下，新建application.yml配置文件。个人感觉YAML相较于properties，在一些方面更好用、更方便一些。
打开配置文件，在里面配置数据库连接相关信息。

```yml
spring:
  datasource:
  driver-class-name: com.mysql.jdbc.Driver
  url: jdbc:mysql://localhost:3306/springboot?useSSL=false
  username: root
  password: mysql

jpa:
  database: mysql
  show-sql: true
```

*此处使用的密码为明文密码，不是很安全，可以使用md5 + salt加密*

## 代码编写

> 文件的创建应该是自下而上的，而代码的编写应该是自上而下的。这让文章写起来很不舒服。现在把文件的创建和代码的编写一次性粘贴出来，所以在编写完全部的代码之前，出现内容找不到报错的问题属于正常现象。以后文章也按照此原则进行。

#### controller层

新建controller包，即使是一个简单的demo，规范还是要有的。
在controller下新建 *UserRestController.java*，至于为什么要加一个Rest呢？可能是我为以后考虑吧。嘻嘻
代码如下

```java
@RestController
@RequestMapping("/user")
public class UserRestController {

}
```

#### entity层

新建entity包，用来存放与数据库字段相对应的内容。

```java
@Data
@Entity
@Table(name = "tb_user")
public class User implements Serializable {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "id")
  private Long id;

  @Column(name = "username")
  private String username;

  @Column(name = "password")
  private String password;

  @Column(name = "age")
  private Integer age;

  @Column(name = "address")
  private String address;
}
```

可以看到此处使用了`@Data`注解，在类中就可以省略掉getter/setter，还是很方面的说。当然这只是lombok的皮毛，暂时够用即可。

#### service层

service层一般放置具体的业务逻辑，在此处新建 *UserService.java* 接口
里面的抽象方法有

```java
public interface UserService {

  List<User> getAll();

  User save(User entity);

  void deleteById(Long id);

  /**
   * 通过id查询用户
   *
   * @param id 用户id（数据库自增）
   */
  Optional<User> findById(Long id);
}
```

在service包下新建impl包，用来实现接口的方法 *UserServiceImpl.java*

```java
@Service
public class UserServiceImpl implements UserService {

  @Autowired
  private UserJPA userJPA;

  @Override
  public List<User> getAll() {
    return userJPA.findAll();
  }

  @Override
  public User save(User entity) {
    return userJPA.save(entity);
  }

  @Override
  public void deleteById(Long id) {
    userJPA.deleteById(id);
  }

  @Override
  public Optional<User> findById(Long id) {
    return userJPA.findById(id);
  }
}
```

#### dao层

dao层即程序与数据库的交互层，在这里编写各种查询语句来满足业务需求。新建 *UserJPA.java*

```java
public interface UserJPA extends JpaRepository<User, Long>,
	JpaSpecificationExecutor<User>, Serializable {
}
```

代码已经写完了。这就是SpringDataJPA的神奇之处。

> 我们UserJPA继承了JpaRepository接口（SpringDataJPA提供的简单数据操作接口）、JpaSpecificationExecutor（SpringDataJPA提供的复杂查询接口）、Serializable（序列化接口）。我们并不需要做其他的任何操作了，因为SpringBoot以及SpringDataJPA会为我们全部搞定，SpringDataJPA内部使用了类代理的方式让继承了它接口的子接口都以spring管理的Bean的形式存在，也就是说我们可以直接使用@Autowired注解在spring管理bean使用

#### controller层

再次回到 *UserRestController.java*，在里面编写具体的增删改查(CRUD)操作。

```java
@RestController
@RequestMapping("/user")
public class UserRestController {

  @Autowired
  private UserServiceImpl userService;

  /**
   * 显示全部用户方法
   */
  @GetMapping("/list")
  public List<User> findAll() {
    return userService.getAll();
  }

  /**
   * 添加、更新用户方法
   */
  @GetMapping("/save")
  public User save(User entity) {
    return userService.save(entity);
  }

  /**
   * 删除用户方法
   *
   * @param id 用户编号
   */
  @GetMapping("/delete")
  public List<User> delete(Long id) {
    userService.deleteById(id);
    return userService.getAll();
  }

  /**
   * 返回指定id用户
   *
   * @param id 用户id
   */
  @GetMapping("{id}")
  public Optional<User> findById(@PathVariable("id") Long id) {
    return userService.findById(id);
  }
}

```

简单的数据库增删改查操作

## 运行测试

启动项目，程序默认端口号为8080，如若想修改，打开 *application.yml*，编写

```yml
server:
  port: 7777


```

即修改程序端口号为7777.在浏览器地址栏输入[http://localhost:7777/user/list][2]
可以看到
![查看全部][3]

由于现在数据库中还没有任何数据，所以内容为空。现在查看程序控制台，由于我们在配置文件中设置了打印SQL语句，所以控制台出现了一条Hibernate定义的查询语句。
![Hibernate生成的查询全部语句][4]

现在新增一条数据

> http://localhost:7777/user/save?username=luvnaxx&password=admin&age=23&address=nanjing
> 可以看到插入成功，页面返回了本条数据的全部内容。
> ![插入数据][5]

再次查询全部数据，会返回当前数据库中的全部数据。删除修改自行测试。

------

- 2018-09-12 20:06:34 新增baseDAO

> 在SpringDataJPA中，如果每个dao层接口都要实现JpaRepository,	JpaSpecificationExecuto以及Serializable，也算是一件比较麻烦的事情，如果定义一个父接口用来实现上述接口，实际的dao层接口只需集成此接口即可，对于dao层接口数量较多的工程，也是一个稍微节约点时间的好方法。
> 在工程中新建base包，新建BaseDAO接口。

```java
@NoRepositoryBean
public interface BaseDAO<T> extends JpaRepository<T, Long>,
        JpaSpecificationExecutor<T>, Serializable {
}


```

添加@NoRepositoryBean注解，此接口就不会作为一个Repository创建代理实现类。
以后的Repository直接集成此接口就可以了

[1]: https://start.spring.io/
[2]: http://localhost:7777/user/list
[3]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201808/4749c7d7fcfd43789004cda8fe4f1c52.png
[4]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201808/01738a950f5e4111b593d389bc58558c.png
[5]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201808/a7dd8002d5f9453c8fde3805adfe559e.png