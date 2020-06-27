# 整合MyBatis

在上一篇SpringDataJPA的基础上进行扩展。

<!--more-->

------

## 数据库准备

还是使用上篇文章中的springboot数据库，这次新建book数据表，

```mysql
CREATE TABLE book
(
  id     int PRIMARY KEY NOT NULL AUTO_INCREMENT,
  name   varchar(20)     NOT NULL,
  author varchar(50),
  price  double          NOT NULL
);
```

## 修改配置文件

在pom.xml中添加MyBatis的依赖

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>1.3.2</version>
</dependency>
```

在application.yml中添加MyBatis的相关设置

```yaml
# Mybatis 配置
mybatis:
  typeAliasesPackage: com.luvnaxx.web.springboot.rookie.entity
  mapperLocations: classpath:mapper/*.xml
```

> typeAliasesPackage定义了数据表对应实体所在的包路径  
> mapperLocations定义配置相关mapper文件的位置  

在resources文件夹中新建mapper文件夹，放置接口对应的mapper文件  
mapper文件的通用模板是

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="xxx">

</mapper>
```

## 代码编写

#### entity层

新建实体类Book.java，并没有什么特殊的注解

```java
import lombok.Data;

@Data
public class Book {

  /**
   * 自增的id
   */
  private Long id;

  /**
   * 书名
   */
  private String name;

  /**
   * 作者
   */
  private String author;

  /**
   * 价格
   */
  private Double price;
}
```

#### controller层

新建BookController.java

```java
@RestController
@RequestMapping("/book")
public class BookController {

  @Autowired
  private BookServiceImpl bookService;

  @GetMapping("/list")
  public List<Book> getAll() {
    return bookService.getAll();
  }

  /**
   * 通过作者名查询该作者全部书籍
   *
   * @param author 作者
   */
  @GetMapping("/author/{author}")
  public List<Book> getAllByAuthor(@PathVariable("author") String author) {
    return bookService.findBooksByAuthor(author);
  }
}
```

#### service层

新建BookService,java

```java
public interface BookService {

  List<Book> findBooksByAuthor(String author);

  List<Book> getAll();
}
```

新建实现类BookServiceImpl.java

```java
@Service
public class BookServiceImpl implements BookService {

  @Autowired
  private BookMapper bookMapper;

  @Override
  public List<Book> findBooksByAuthor(String author) {
    return bookMapper.findBooksByAuthor(author);
  }

  @Override
  public List<Book> getAll() {
    return bookMapper.getAll();
  }
}
```

#### dao层

新建BookMapper.java

```java
public interface BookMapper {

  List<Book> findBooksByAuthor(String author);

  List<Book> getAll();

  Book findBookByBookName(String name);
}


```

为了区分SpringDataJPA和MyBatis，这里把dao层又向下分了jpa和mapper两个包。当然实际项目中应该不会同时用到两种ORM。

```html
dao
|--jpa
|   |--UserJPA.java
|--mapper
    |--BookMapper.java


```

#### 新建mapper文件

此时并没有相关SQL语句的代码实现，SQL语句可以使用注解形式编写在方法上面，也可以使用xml文件形式。  
在resource/mapper文件夹下新建BookMapper.xml文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.luvnaxx.web.springboot.rookie.dao.mapper.BookMapper">

  <select id="findBooksByAuthor"
    resultType="com.luvnaxx.web.springboot.rookie.entity.Book">
    select * from book where author = #{author};
  </select>
  <select id="getAll" resultType="com.luvnaxx.web.springboot.rookie.entity.Book">
    select * from book;
  </select>
  <select id="findBookByBookName"
    resultType="com.luvnaxx.web.springboot.rookie.entity.Book">
    select * from book where name = #{name};
  </select>
</mapper>

```

> namespace就是dao层的接口文件。使用相关插件可以很方便的编写SQL语句以及进行文件之间的跳转。