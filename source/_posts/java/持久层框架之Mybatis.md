---
title: ORM 框架之 Mybatis
tags: 
    - mybatis
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/mybatis/mybtis01.jpg
# excerpt: 持久层框架
categories:
    - 后端
    - java
---

## 1. 简介

`MyBatis` 是⼀款优秀的基于 ORM 的半⾃动轻量级持久层框架，它⽀持定制化 SQL、存储过程以及⾼级映
射。MyBatis 避免了⼏乎所有的JDBC代码和⼿动设置参数以及获取结果集。MyBatis 可以使⽤简单的
XML或注解来配置和映射原⽣类型、接⼝和 Java 的 POJO （Plain Old Java Objects,普通⽼式 Java 对象）
为数据库中的记录。

**Tips:** [官网地址](https://mybatis.org/mybatis-3/)

## 2. 急速入门

### 2.1 引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>pro.fengjian</groupId>
    <artifactId>MybatisQuickStart</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>

        <!-- mysql 驱动类-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>

        <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.9</version>
        </dependency>

        <!-- 单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>

        <!-- log4j 日志 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>utf-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 2.2 定义实体类

```java
/**
 * @作者 风间
 * @创建时间 2022/4/17 20:55
 */

public class User {

    private Integer id;
    private String username;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}

```

### 2.3 新建实体类对应的表

```sql
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8mb4;
```

### 2.4 核心配置文件

#### 2.4.1 SqlConfig.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

**<font color="red">提示：</font>**

还可以将数据源的配置单独放到外部配置文件中，比如 jdbc.properties

```properties
jdbc.mysql.driver = com.mysql.cj.jdbc.Driver
jdbc.mysql.url = jdbc:mysql://localhost:3306/mybatis
jdbc.mysql.user = root
jdbc.mysql.password = 123456
```

那么相应的 SqlMapConfig.xml 修改后如下:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    
    <!--外部配置文件-->
    <properties resource="jdbc.properties"/>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.mysql.driver}"/>
                <property name="url" value="${jdbc.mysql.url}"/>
                <property name="username" value="${jdbc.mysql.user}"/>
                <property name="password" value="${jdbc.mysql.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

#### 2.4.2 mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="UserMapper">
    <select id="findAll" resultType="User">
        select * from User
    </select>
</mapper>
```

### 2.3 编写dao

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:18
 */
public class UserDao {

    List<User> findAll() throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        List<User> userList = sqlSession.selectList("UserMapper.findAll");
        sqlSession.close();
        return userList;
    }
}

```

**<font color="red">提示：</font>**
上述 UserDao 的方式有一个冗余的部分`UserMapper.findAll`，每增加一个方法，都需要指定 Mapper 和 方法；
mybatis利用代理模式做了优化，约定如下：
1）编写接口，接口全路径即 namespace 
2）接口方法即 sql 的 id
3）接口方法返回值即 sql 的 resultType
4）接口方法的参数即 sql 的 parameterType

所以我们可以修改 UserDao 代码为 UserMapper：

```java
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:43
 */

public interface UserMapper {

    List<User> findAll();
}
```



### 2.4 测试

#### UserDao 方式（传统写法）

```java
import java.io.IOException;
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:19
 */
public class Test {

    public static void main(String[] args) throws IOException {
        UserDao userDao = new UserDao();
        List<User> userList = userDao.findAll();
        for (User user : userList) {
            System.out.println(user);
        }

    }
}

```

#### UserMapper 方式（推荐）

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:19
 */
public class Test {

    public static void main(String[] args) throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = mapper.findAll();
        for (User user : userList) {
            System.out.println(user);
        }

    }
}

```

## 3. 复杂查询

### 3.1 mybatis 之一对一查询

查询订单即订单所属用户：

#### 3.1.1 实体类

1. User
      
```java
package pro.fengjian;

/**
 * @作者 风间
 * @创建时间 2022/4/17 20:55
 */

public class User {

    private Integer id;
    private String username;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}

```

2. Order

OrderMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pro.fengjian.OrderMapper">

    <resultMap id="orderMap" type="pro.fengjian.Order">
        <result column="id" property="id"/>
        <result column="order_time" property="order_time"/>
        <result column="total" property="total"/>
        <association property="user" javaType="pro.fengjian.User">
            <result column="uid" property="id"/>
            <result column="username" property="username"/>
        </association>
    </resultMap>

    <select id="findAll" resultMap="orderMap">
        select * from `order` o,user u where o.uid=u.id;
    </select>
</mapper>
```

#### 3.1.2 Mapper 接口

```java
/**
 * @作者 风间
 * @创建时间 2022/4/17 22:41
 */
package pro.fengjian;

import java.util.List;

public interface OrderMapper {

    List<Order> findAll();
}

```

### 3.2 mybatis 之一对多查询

查询用户及用户的订单：

#### 3.2.1 实体类

1. User
   
```java
package pro.fengjian;

import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 20:55
 */

public class User {

    private Integer id;
    private String username;
    
    private List<Order> order_list;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                '}';
    }
}

```

2. Order
   
```java
/**
 * @作者 风间
 * @创建时间 2022/4/17 22:07
 */
package pro.fengjian;

import java.util.Date;

public class Order {

    private Integer id;
    private Date order_time;
    private double total;

    private User user;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public Date getOrder_time() {
        return order_time;
    }

    public void setOrder_time(Date order_time) {
        this.order_time = order_time;
    }

    public double getTotal() {
        return total;
    }

    public void setTotal(double total) {
        this.total = total;
    }

    public User getUser() {
        return user;
    }

    public void setUser(User user) {
        this.user = user;
    }

    @Override
    public String toString() {
        return "Order{" +
                "id=" + id +
                ", order_time=" + order_time +
                ", total=" + total +
                ", user=" + user +
                '}';
    }
}

```
   
UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="pro.fengjian.UserMapper">

    <resultMap id="userMap" type="pro.fengjian.User">
        <result property="id" column="id"/>
        <result property="username" column="username"/>
        <collection property="order_list" ofType="pro.fengjian.Order">
            <result property="id" column="oid"/>
            <result property="order_time" column="order_time"/>
            <result property="total" column="total"/>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="userMap">
        select *,o.id oid from user u left join `order` o on u.id=o.uid
    </select>
</mapper>
```


### 3.3 mybatis 之多对多查询

查询用户及拥有的角色：

涉及的表 user role user_role(中间表)

UserMapper.xml

```xml
<resultMap id="userRoleMap" type="pro.fengjian.User">
    <result property="id" column="id"/>
    <result property="username" column="username"/>
    <collection property="role_list" ofType="pro.fengjian.Role">
        <result property="id" column="rid"/>
        <result property="name" column="name"/>
    </collection>
</resultMap>
<select id="findAllUserWithRole" resultMap="userRoleMap">
    select u.*,r.*,r.id rid from user u left join user_role ur on u.id=ur.userid
                                        inner join role r on ur.roleid=r.id;
</select>

```

## 4. 缓存

### 一级缓存：基于 sqlSession

1. 同一个 sqlSession 两次查询，mybatis 会直接从缓存中获取
2. 若两次查询中间涉及增、删、改，则缓存失效（sqlSession 执行了 commit 操作）

### 二级缓存：基于 Mapper 文件的 namespace

1. 二级缓存需要手动开启
2. 若两次查询中间涉及增、删、改，则缓存失效（sqlSession 执行了 commit 操作）

## 5. 插件

mybatis 插件 涉及四大组件：Executor、StatementHandler、ParameterHandler、ResultSetHandler

### 5.1 自定义插件

#### 5.1.1 定义插件

```java
/**
 * @作者 风间
 * @创建时间 2022/4/18 08:54
 */
package pro.fengjian;

import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.plugin.*;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;

import java.util.Properties;

@Intercepts({
        @Signature(
                type = Executor.class,
                method = "query",
                args = {MappedStatement.class, Object.class, RowBounds.class, ResultHandler.class}
        )
})
public class MyPlugin implements Interceptor {

    // 增强逻辑书写部分
    public Object intercept(Invocation invocation) throws Throwable {
        System.out.println("对query方法进行增强");
        return invocation.proceed();
    }

    // 将自定义插件放入拦截器链
    public Object plugin(Object target) {
        System.out.println("增强的目标对象："+target);
        return Plugin.wrap(target,this);
    }

    // 自定义插件初始化属性
    public void setProperties(Properties properties) {
        System.out.println("初始化属性："+properties);
    }
}

```

#### 5.1.2 注册插件

```xml
<!--插件注册-->
<plugins>
    <plugin interceptor="pro.fengjian.MyPlugin">
        <property name="key1" value="value1"/>
        <property name="key2" value="value2"/>
    </plugin>
</plugins>
```

### 5.2 通用 mapper

> 提供一些通用的 sql 以便快速开发

#### 5.2.1 引入依赖

```xml


```

#### 5.2.2 注册插件

```xml
<plugin interceptor="tk.mybatis.mapper.mapperhelper.MapperInterceptor">
    <!-- 通⽤Mapper接⼝，多个通⽤接⼝⽤逗号隔开 -->
    <property name="mappers" value="tk.mybatis.mapper.common.Mapper"/>
</plugin>
```

#### 5.2.3 插件使用

1. 实体类
   
```java
package pro.fengjian;

import javax.persistence.Transient;
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 20:55
 */

public class User {

    private Integer id;
    private String username;

    @Transient
    private List<Order> order_list;
    @Transient
    private List<Role> role_list;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public List<Order> getOrder_list() {
        return order_list;
    }

    public void setOrder_list(List<Order> order_list) {
        this.order_list = order_list;
    }

    public List<Role> getRole_list() {
        return role_list;
    }

    public void setRole_list(List<Role> role_list) {
        this.role_list = role_list;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", order_list=" + order_list +
                ", role_list=" + role_list +
                '}';
    }
}

```

**<font color="red">提示：</font>**

不需要查询的字段使用 `@Transient` 注解标记

2. Mapper 接口

```java
package pro.fengjian;

import tk.mybatis.mapper.common.Mapper;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:43
 */

public interface UserMapper extends Mapper<User> {

   
}

```

3. 测试

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import pro.fengjian.User;
import pro.fengjian.UserMapper;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:19
 */
public class Test {

    public static void main(String[] args) throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = new User();
        user.setId(1);
        List<User> userList = mapper.select(user);
        System.out.println(userList);

    }
}

```

### 5.3 分页插件

#### 5.3.1 引入依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>3.7.5</version>
</dependency>
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>0.9.1</version>
</dependency>
```

#### 5.3.2 注册插件

> 分页插件应先于通用 mapper 插件注册，注意注册顺序

```xml
<plugin interceptor="com.github.pagehelper.PageHelper">
    <property name="dialect" value="mysql"/>
</plugin>
```

#### 5.3.2 测试

```java
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import pro.fengjian.User;
import pro.fengjian.UserMapper;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * @作者 风间
 * @创建时间 2022/4/17 21:19
 */
public class Test {

    public static void main(String[] args) throws IOException {
        InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        PageHelper.startPage(1,10);
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> orderList = mapper.findAll();
        PageInfo<User> userPageInfo = new PageInfo<User>(orderList);
        System.out.println(userPageInfo.getTotal());
    }
}

```

## 6. mybatis-plus 的使用

> MyBatis-Plus（简称 MP）是⼀个 MyBatis 的增强⼯具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提⾼效率⽽⽣

**<font color="red">提示：</font>**
引入 mybatis-plus 后不要再次引入 mybatis 及 mybatis-spring 依赖，以免版本问题导致异常问题

### 6.1 急速入门( SpringBoot项目 )

#### 6.1.1 引入依赖
   
```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.1.1</version>
</dependency>
```

#### 6.1.2 编写实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    
    private Integer id;
    private String username;
}

```

#### 6.1.3 编写 mapper

```java
public interface UserMapper extends BaseMapper<User> {
}

```

#### 6.1.4 扫描 mapper

```java
@MapperScan("pro.fengjian.springbootmybatisplus.mapper")
@SpringBootApplication
public class SpringbootMybatisPlusApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootMybatisPlusApplication.class, args);
    }

}
```

#### 6.1.5 测试

```java
@SpringBootTest
class SpringbootMybatisPlusApplicationTests {

    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        List<User> userList = userMapper.selectList(null);
        System.out.println(userList);
    }

}
```

### 6.2 常用的一些注解释义

`@TableName("tb_user")`: 若数据库表名与实体类名称不一致，可以手动指定表名

`@TableId(type = IdType.AUTO)`: 主键 id 生成策略

* AUTO (自增)
* NONE (未设置)
* INPUT (用户输入)
* ID_WORKER (全局唯一)
* UUID (全局唯一)
* ID_WORKER_STR (全局唯一)

`@TableField`:

* 对象中的属性名和字段名不⼀致的问题（⾮驼峰)
* 对象中的属性字段在表中不存在的问题
* 字段不加⼊查询字段

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    @TableField(select = false)
    private Integer id;

    @TableField(value = "username")
    private String name;

    @TableField(exist = false)
    private String email;
}
```

### 6.3 基本配置

#### 基础配置

1. configLocation 指定配置文件位置
  
```properties
mybatis-plus.config-location = classpath:mybatis-config.xml
```

2. mapperLocations 指定 mapper 文件位置

```properties
mybatis-plus.mapper-locations = classpath*:mybatis/*.xml
```

#### 进阶配置

1. mapUnderscoreToCamelCase: 自动转驼峰，默认 true

```properties
#关闭⾃动驼峰映射，该参数不能和mybatis-plus.config-location同时存在
mybatis-plus.configuration.map-underscore-to-camel-case=false
```

2. cacheEnabled： 开启缓存，默认 true

```properties
mybatis-plus.configuration.cache-enabled=false
```

### DB 配置

1. idType: 全局默认主键类型，不用每个实体类配置了

```properties
mybatis-plus.global-config.db-config.id-type=auto
```

2. tablePrefix：全局表前缀

```properties
mybatis-plus.global-config.db-config.table-prefix=tb_
```

### 6.4 QueryWrapper 条件构造器

#### 6.4.1 基本比较

`allEq`:

方法重载：

```java
allEq(Map<R, V> params)
allEq(Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, Map<R, V> params, boolean null2IsNull)
```

参数说明：
params ： key 数据库字段，value 查询的值
null2IsNull： 是否将 params 中的 null 值转为 sql 中的 is null，false 表示忽略
condition： true 则进行条件构造，false 不进行

```java
allEq(BiPredicate<R, V> filter, Map<R, V> params)
allEq(BiPredicate<R, V> filter, Map<R, V> params, boolean null2IsNull)
allEq(boolean condition, BiPredicate<R, V> filter, Map<R, V> params, boolean
null2IsNull)
```

参数说明：
filter： 对 params 进行过滤

`eq`: =

`ne`: <>

`gt`: >

`ge`: >=

`lt`: <

`le`: <=

`between`: between 值 1 and 值 2

`notBetween`: not between 值 1 and 值 2

`in`: 字段 in （值1，值2,...）

`notin`: 字段 not in (值1，值2，...)

**<font color="red">提示：</font>**

LambdaQueryWrapper 支持链式调用！！！

#### 6.4.2 模糊查询

`like`: like('name','jack') -> name like '%jack%'
`notlike`: notlike('name','jack') -> name not like '%jack%'
`likeleft`: likeleft('name','jack') -> name like '%jack'
`likeright`: likeright('name','jack') -> name like 'jack%'

#### 6.4.2 排序

`orderBy`: orderBy(true, true, "id", "name") ---> order by id ASC,name ASC
`orderByDesc`: orderByAsc("id", "name") ---> order by id ASC,name ASC
`orderByAsc`: orderByDesc("id", "name") ---> order by id DESC,name DESC

#### 6.4.3 or and

多条件默认 and 连接，使用 or() 则使用 or 拼接sql

#### 6.4.4 select 指定查询字段

select('name','age') 指定查询的字段，默认是全字段

### 6.5 常用插件

#### 6.5.1 插件注册

1. 方式一：注入到 Spring 容器

```java
@Bean
public MyInterceptor myInterceptor(){
    return new MyInterceptor();
}
```

2. 方式二：mybatis 核心配置文件中注册

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration> 
    <plugins>
        <plugin interceptor="com.lagou.mp.plugins.MyInterceptor"></plugin>
    </plugins>
</configuration>
```

#### 6.5.2 执行分析插件

> 开发环境使用

```java
// 执行分析插件，阻断全表更新、删除操作
@Bean
public SqlExplainInterceptor sqlExplainInterceptor(){
    SqlExplainInterceptor sqlExplainInterceptor = new SqlExplainInterceptor();
    List<ISqlParser> sqlParserList = new ArrayList<>();
    // 攻击 sql 阻断解析器，加入解析链
    sqlParserList.add(new BlockAttackSqlParser());
    sqlExplainInterceptor.setSqlParserList(sqlParserList);
    return sqlExplainInterceptor;
}   
```

当执⾏全表更新时，会抛出异常，这样有效防⽌了⼀些误操作.

#### 6.5.3 性能分析插件

> 开发环境使用

```java
// 性能分析插件
@Bean
public PerformanceInterceptor performanceInterceptor(){
    PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
    performanceInterceptor.setFormat(true);
    performanceInterceptor.setMaxTime(1);// 单位：ms
    return performanceInterceptor;
}
```

#### 6.5.4 乐观锁插件

* 取出记录时，获取当前version
* 更新时，带上这个version
* 执⾏更新时， set version = newVersion where version = oldVersion
* 如果version不对，就更新失败

```java
// 乐观锁插件
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor(){
        return new OptimisticLockerInterceptor();
    }
```

数据表增加 `version` 字段

```sql
ALTER TABLE `user`
ADD COLUMN `version` int(10) NULL AFTER `email`;
UPDATE `user` SET `version`='1';
```

实体类增加 `@Version` 注解

```java
@Version
private Integer version;
```

### 6.5 Sql 注入器，扩充 BaseMapper

> BaseMapper 提供的默认方法不够用了，自己搞

原理：mybatis-plus 利用 AbstractSqlInjector 将 BaseMapper 中方法注入到了 mybatis 中

#### 6.5.1 MyBaseMapper

```java
public interface MyBaseMapper<T> extends BaseMapper<T> {

    List<T> findAll();
}
```

#### 6.5.2 MySqlInjector

```java
public class MysqlInjector extends DefaultSqlInjector {

    @Override
    public List<AbstractMethod> getMethodList() {
        List<AbstractMethod> methodList = super.getMethodList();
        methodList.add(new FindAll());
        return methodList;
    }
}
```

#### 6.5.3 FindAll

```java
public class FindAll extends AbstractMethod {

    @Override
    public MappedStatement injectMappedStatement(Class<?> mapperClass, Class<?> modelClass, TableInfo tableInfo) {

        String sqlMethod = "findAll";
        String sql = "select * from " + tableInfo.getTableName();
        SqlSource sqlSource = languageDriver.createSqlSource(configuration, sql, modelClass);
        return this.addSelectMappedStatement(mapperClass, sqlMethod, sqlSource, modelClass, tableInfo);
    }
}
```

#### 6.5.4 添加到 Spring 容器

```java
@Configuration
public class InjectorConfiguration {

    @Bean
    public MysqlInjector mysqlInjector(){
        return new MysqlInjector();
    }
}
```

**<font color="red">提示:</font>**

如果直接继承AbstractSqlInjector的话，原有的BaseMapper中的⽅法将失效，所以我们选择继承
DefaultSqlInjector进⾏扩展.

### 6.6 逻辑删除

1. 修改表结构,添加 `deleted` 字段

```sql
ALTER TABLE `user`
ADD COLUMN `deleted` int(1) NULL DEFAULT 0 COMMENT '1代表删除，0代表未删除'
AFTER `version`;
```

2. 实体类添加 `@TableLogic` 注解

```java
@TableLogic
private Integer deleted;
```

3. 新增配置，指定逻辑删除字段的值

```properties
# 逻辑已删除值(默认为 1)
mybatis-plus.global-config.db-config.logic-delete-value=1
# 逻辑未删除值(默认为 0)
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

### 6.7 基于 mapper 接口的 idea 插件

mybatisx 实现：

1）java 与 XML 代码互跳
2）自动生成 statement

__完__
