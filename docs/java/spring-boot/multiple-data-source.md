# 配置多数据源的三种方式

很早就想记录一下Spring Boot配置多数据的方式。但是网上找到的大多还都是Spring Boot 1.x的内容，一些东西已经不适合现在的版本了。这几天又仔细找了一找，总算把Spring Boot 2配置多数据源的几种方式实验通过了。以两个数据源为例

<!--more-->

------

## 数据库概况

共有两个数据库，这里以两个MySQL数据库为例。实际中可以任意组合。在我工作环境中，常用到的是位于不同机器上面的MySQL加上SQL Server的组合

数据表基本情况如图
![多数据源数据库信息][1]

## JdbcTemplate

结构如图
![项目结构图][2]

#### 创建项目，引入依赖

```xml
  <dependencies>

    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.2</version>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
    </dependency>
      
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <optional>true</optional>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
```

#### 编写配置文件

配置文件**application.yml**填写两个数据源的基本信息

```yml
spring:
  datasource:
    primary:
      jdbc-url: jdbc:mysql://212.64.52.104:3306/spring?userSSL=false
      driver-class-name: com.mysql.jdbc.Driver
      username: xxxx
      password: xxxx
    secondary:
      jdbc-url: jdbc:mysql://212.64.52.104:3306/copy?userSSL=false
      driver-class-name: com.mysql.jdbc.Driver
      username: xxxxx
      password: xxxxx
```

#### 编写代码

新建`config`包，新建`DataSourceConfig.java`

```java
@Configuration
public class DataSourceConfig {

    @Bean("primary")
    @Primary
    @Qualifier("primary")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    public DataSource dataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean("secondary")
    @Qualifier("secondary")
    @ConfigurationProperties(prefix = "spring.datasource.secondary")
    public DataSource dataSourceSlave() {
        return DataSourceBuilder.create().build();
    }

    // 支持JdbcTemplate实现多数据源
    @Bean(name = "primaryJdbcTemplate")
    public JdbcTemplate primaryJdbcTemplate(
            @Qualifier("primary") DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }


    @Bean(name = "secondaryJdbcTemplate")
    public JdbcTemplate secondaryJdbcTemplate(
            @Qualifier("secondary") DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
```

#### 测试

编写测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class JdbcApplicationTests {

    @Autowired
    @Qualifier("primaryJdbcTemplate")
    private JdbcTemplate primaryJdbcTemplate;

    @Autowired
    @Qualifier("secondaryJdbcTemplate")
    private JdbcTemplate secondaryJdbcTemplate;

    @Test
    public void contextLoads() {
        List<Map<String, Object>> list = primaryJdbcTemplate.queryForList("select * from technologies;");
        System.out.println(list);

        List<Map<String, Object>> list2 = secondaryJdbcTemplate.queryForList("select * from test;");
        System.out.println(list2);
    }

}
```

------



## JPA

结构如图
![项目结构图][3]

#### 创建项目，引入依赖

```xml
  <dependencies>

    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.2</version>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
    </dependency>
      
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <optional>true</optional>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
```

#### 编写配置文件

配置文件**application.yml**填写两个数据源的基本信息

```yml
spring:
  datasource:
    primary:
      jdbc-url: jdbc:mysql://212.64.52.104:3306/spring?userSSL=false
      driver-class-name: com.mysql.jdbc.Driver
      username: xxxx
      password: xxxx
    secondary:
      jdbc-url: jdbc:mysql://212.64.52.104:3306/copy?userSSL=false
      driver-class-name: com.mysql.jdbc.Driver
      username: xxxxx
      password: xxxxx
      
  jpa:
    database: mysql
    generate-ddl: true
    show-sql: true
    hibernate:
      ddl-auto: update
      naming:
        physical-strategy: org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy
```

#### 编写代码

新建`config`包，新建`DataSourceConfig.java`

```java
@Configuration
public class DataSourceConfig {

    @Bean(name = "primaryDataSource")
    @Qualifier("primaryDataSource")
    @Primary
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean(name = "secondaryDataSource")
    @Qualifier("secondaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.secondary")
    public DataSource secondaryDataSource() {
        return DataSourceBuilder.create().build();
    }
}
```

新建`PrimaryConfig.java`

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef = "entityManagerFactoryPrimary",
        transactionManagerRef = "transactionManagerPrimary",
        basePackages = {"com.luvnaxx.data.jpa.repository.primary"})
public class PrimaryConfig {

    @Resource
    @Qualifier("primaryDataSource")
    private DataSource primaryDataSource;
    @Resource
    private JpaProperties jpaProperties;

    @Primary
    @Bean(name = "entityManagerPrimary")
    public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
        return entityManagerFactoryPrimary(builder).getObject().createEntityManager();
    }

    private Map<String, Object> getVendorProperties() {
        return jpaProperties.getHibernateProperties(new HibernateSettings());
    }

    /**
     * 设置实体类所在位置
     */
    @Primary
    @Bean(name = "entityManagerFactoryPrimary")
    public LocalContainerEntityManagerFactoryBean entityManagerFactoryPrimary(EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(primaryDataSource)
                .packages("com.luvnaxx.data.jpa.entity.primary")
                .persistenceUnit("primaryPersistenceUnit")
                .properties(getVendorProperties())
                .build();
    }

    @Primary
    @Bean(name = "transactionManagerPrimary")
    public PlatformTransactionManager transactionManagerPrimary(EntityManagerFactoryBuilder builder) {
        return new JpaTransactionManager(entityManagerFactoryPrimary(builder).getObject());
    }

}
```

新建`SecondaryConfig.java`

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef = "entityManagerFactorySecondary",
        transactionManagerRef = "transactionManagerSecondary",
        basePackages = {"com.luvnaxx.data.jpa.repository.secondary"})
public class SecondaryConfig {

    @Resource
    @Qualifier("secondaryDataSource")
    private DataSource secondaryDataSource;
    @Resource
    private JpaProperties jpaProperties;

    @Bean(name = "entityManagerSecondary")
    public EntityManager entityManager(EntityManagerFactoryBuilder builder) {
        return entityManagerFactorySecondary(builder).getObject().createEntityManager();
    }

    private Map<String, Object> getVendorProperties() {
        return jpaProperties.getHibernateProperties(new HibernateSettings());
    }

    /**
     * 设置实体类所在位置
     */
    @Bean(name = "entityManagerFactorySecondary")
    public LocalContainerEntityManagerFactoryBean entityManagerFactorySecondary(EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(secondaryDataSource)
                .packages("com.luvnaxx.data.jpa.entity.secondary")
                .persistenceUnit("secondaryPersistenceUnit")
                .properties(getVendorProperties())
                .build();
    }

    @Bean(name = "transactionManagerSecondary")
    public PlatformTransactionManager transactionManagerSecondary(EntityManagerFactoryBuilder builder) {
        return new JpaTransactionManager(entityManagerFactorySecondary(builder).getObject());
    }

}
```

两者的区别就是`PrimaryConfig.java`有`@Primary`注解

新建`entity.primary`包以及`entity.secondary`包，新建`TechnologiesEntity.java`和`TestEntity.java`

```java
@Entity
@Table(name = "technologies")
@Data
public class TechnologiesEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Integer id;

    @Column(name = "name")
    private String name;

    @Column(name = "type")
    private String type;
}
```

```java
@Entity
@Table(name = "test")
@Data
public class TestEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Integer id;

    @Column(name = "name")
    private String name;

    @Column(name = "gender")
    private String gender;
}
```

新建`base`包以及`BaseDAO.java`

```java
@NoRepositoryBean
public interface BaseDAO<T> extends JpaRepository<T, Long>,
        JpaSpecificationExecutor<T>, Serializable {

}
```

新建`repository`包，只做简单测试

```java
@Repository
public interface TechnologiesJPA extends BaseDAO<TechnologiesEntity> {
}
```

```java
@Repository
public interface TestJPA extends BaseDAO<TestEntity> {
}
```

#### 测试

编写测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class JpaApplicationTests {

    @Autowired
    private TechnologiesJPA technologiesJPA;

    @Autowired
    private TestJPA testJPA;

    @Test
    public void contextLoads() {

        List<TechnologiesEntity> technologies = technologiesJPA.findAll();
        System.out.println(technologies);

        List<TestEntity> tests = testJPA.findAll();
        System.out.println(tests);

    }

}
```

------



## MyBatis

结构如图
![项目结构图][4]

#### 创建项目，引入依赖

```xml
  <dependencies>

    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>1.3.2</version>
    </dependency>

    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
    </dependency>
      
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.0.0</version>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <optional>true</optional>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <scope>runtime</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
```

#### 编写配置文件

配置文件**application.yml**填写两个数据源的基本信息

```yml
spring:
  datasource:
    primary:
      jdbc-url: jdbc:mysql://212.64.52.104:3306/spring?userSSL=false
      driver-class-name: com.mysql.jdbc.Driver
      username: xxxx
      password: xxxx
    secondary:
      jdbc-url: jdbc:mysql://212.64.52.104:3306/copy?userSSL=false
      driver-class-name: com.mysql.jdbc.Driver
      username: xxxxx
      password: xxxxx

```

#### 编写代码

新建`config`包，新建`PrimaryDataSourceConfig.java`和`SecondaryDataSourceConfig.java`

```java
@Configuration
@MapperScan(basePackages = "com.luvnaxx.data.mybatis.mapper.primary",sqlSessionTemplateRef = "primarySqlSessionTemplate")
public class PrimaryDataSourceConfig {
    /**
     * 创建数据源
     *@return DataSource
     */
    @Bean(name = "primaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.primary")
    @Primary
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create().build();
    }

    /**
     * 创建工厂
     *@param dataSource
     *@throws Exception
     *@return SqlSessionFactory
     */
    @Bean(name = "primarySqlSessionFactory")
    @Primary
    public SqlSessionFactory primarySqlSessionFactory(@Qualifier("primaryDataSource") DataSource dataSource) throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath:mapper/primary/*.xml"));
        return bean.getObject();
    }

    /**
     * 创建事务
     *@param dataSource
     *@return DataSourceTransactionManager
     */
    @Bean(name = "primaryTransactionManager")
    @Primary
    public DataSourceTransactionManager primaryDataSourceTransactionManager(@Qualifier("primaryDataSource") DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    /**
     * 创建模板
     *@param sqlSessionFactory
     *@return SqlSessionTemplate
     */
    @Bean(name = "primarySqlSessionTemplate")
    @Primary
    public SqlSessionTemplate primarySqlSessionTemplate(@Qualifier("primarySqlSessionFactory") SqlSessionFactory sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }

}

```

```java
@Configuration
@MapperScan(basePackages = "com.luvnaxx.data.mybatis.mapper.secondary", sqlSessionTemplateRef = "secondarySqlSessionTemplate")
public class SecondaryDataSourceConfig {

    /**
     * 创建数据源
     *
     * @return DataSource
     */
    @Bean(name = "secondaryDataSource")
    @ConfigurationProperties(prefix = "spring.datasource.secondary")
    public DataSource secondaryDataSource() {
        return DataSourceBuilder.create().build();
    }

    /**
     * 创建工厂
     *
     * @param dataSource
     * @return SqlSessionFactory
     * @throws Exception
     */
    @Bean(name = "secondarySqlSessionFactory")
    public SqlSessionFactory secondarySqlSessionFactory(@Qualifier("secondaryDataSource") DataSource dataSource) throws 
            Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);
        bean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath:mapper/secondary/*" +
                ".xml"));
        return bean.getObject();
    }

    /**
     * 创建事务
     *
     * @param dataSource
     * @return DataSourceTransactionManager
     */
    @Bean(name = "secondaryTransactionManager")
    public DataSourceTransactionManager secondaryDataSourceTransactionManager(@Qualifier("secondaryDataSource") DataSource
                                                                                       dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }

    /**
     * 创建模板
     *
     * @param sqlSessionFactory
     * @return SqlSessionTemplate
     */
    @Bean(name = "secondarySqlSessionTemplate")
    public SqlSessionTemplate secondarySqlSessionTemplate(@Qualifier("secondarySqlSessionFactory") SqlSessionFactory 
                                                                   sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }

}

```

创建`mapper`包

```java
@Mapper
public interface TechnologiesMapper {

    List<TechnologiesEntity> getAll();
}

```

```java
@Mapper
public interface TestMapper {

    List<TestEntity> getAll();
}

```

创建`model`包

```java
@Data
public class TechnologiesEntity implements Serializable {

    private Integer id;

    private String name;

    private String type;
}

```

```java
@Data
public class TestEntity implements Serializable {

    private Integer id;

    private String name;

    private String gender;
}

```

#### MyBatis配置文件

在resources目录下创建Mapper.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.luvnaxx.data.mybatis.mapper.primary.TechnologiesMapper" >
  <!-- 结果映射 -->
  <resultMap id="BaseMap" type="com.luvnaxx.data.mybatis.model.TechnologiesEntity">
    <id column="id" property="id" jdbcType="INTEGER" />
    <result column="name" property="name" jdbcType="VARCHAR" />
    <result column="type" property="type" jdbcType="VARCHAR" />
  </resultMap>

  <!-- 查询所有数据 -->
  <select id="getAll" resultMap="BaseMap">
    SELECT * FROM technologies;
  </select>

</mapper>

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.luvnaxx.data.mybatis.mapper.secondary.TestMapper" >
  <!-- 结果映射 -->
  <resultMap id="BaseMap" type="com.luvnaxx.data.mybatis.model.TestEntity">
    <id column="id" property="id" jdbcType="INTEGER" />
    <result column="name" property="name" jdbcType="VARCHAR" />
    <result column="gender" property="gender" jdbcType="VARCHAR" />
  </resultMap>

  <!-- 查询所有数据 -->
  <select id="getAll" resultMap="BaseMap">
    SELECT * FROM test;
  </select>

</mapper>

```

#### 测试

编写测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MybatisApplicationTests {

    @Autowired
    private TechnologiesMapper technologiesMapper;

    @Autowired
    private TestMapper testMapper;

    @Test
    public void contextLoads() {
        List<TechnologiesEntity> technologiesEntityList = technologiesMapper.getAll();
        System.out.println(technologiesEntityList);

        List<TestEntity> testEntityList = testMapper.getAll();
        System.out.println(testEntityList);

    }
}
```

[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2018/12/ea22c60d150a433ab2a2ca1cb660925f.png
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2018/12/9e71b329dc014b6da8d90c1d3931c1bf.png
[3]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2018/12/24f9f1c744f7481580512649caa19568.png
[4]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2018/12/c0421bc760a44bac8e8f581d8fcfe7c2.png

  