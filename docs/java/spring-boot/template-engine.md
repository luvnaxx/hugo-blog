## FreeMarker

#### 代码

<!--more-->

###### 依赖`pom.xml`

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

###### 配置`application.yml`

```yml
spring:
  # FreeMarker 配置
  freemarker:
    cache: false
    charset: utf-8
    check-template-location: true
    content-type: text/html
    expose-request-attributes: true
    expose-session-attributes: true
    request-context-attribute: request
```

###### 路由`PageController.java`

```java
@GetMapping("/book/all")
public String findAllBooks(Model model) {
    List<BookEntity> bookEntities = bookService.getAll();
    model.addAttribute("bookList", bookEntities);
    return "bookList";
}
```

###### 页面`bookList.ftl`

```html
<!DOCTYPE html>

<html lang="en">
<body>
<#list bookList as bookEntity>
书名：${bookEntity.name} <br>
作者：${bookEntity.author} <br>
售价：${bookEntity.price} <br>
</#list>
</body>

</html>
```

#### 效果

![FreeMarker][1]

## Thymeleaf

#### 代码

###### 依赖`pom.xml`

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
  <groupId>net.sourceforge.nekohtml</groupId>
  <artifactId>nekohtml</artifactId>
  <version>1.9.22</version>
</dependency>
```

###### 配置`application.yml`

```yml
spring:
  # Thymeleaf 配置
  thymeleaf:
    mode: LEGACYHTML5
    encoding: utf-8
    servlet:
      content-type: text/html
    # 开发时关闭缓存，不然无法看到实时页面
    cache: false
```

###### 路由`PageController.java`

```java
@GetMapping("/thymeleaf")
public String thymeleaf(Model model) {
    List<BookEntity> bookEntities = bookService.getAll();
    model.addAttribute("bookList", bookEntities);
    return "thymeleaf";
}
```

###### 页面`thymeleaf.html`

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>thymeleaf测试</title>
</head>
<body>
<table>
    <tr>
        <th>书名</th>
        <th>作者</th>
        <th>价格</th>
    </tr>
    <tr th:each="bookEntity : ${bookList}">
        <td th:text="${bookEntity.name}">1</td>
        <td th:text="${bookEntity.author}">海</td>
        <td th:text="${bookEntity.price}">18</td>
    </tr>
</table>
</body>
</html>
```

#### 效果

![Thymeleaf][2]

[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201809/9f15397d0f874d8382d6e692af64e7ee.PNG
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201809/989d4356914c4f16902e87d60e9964a4.PNG