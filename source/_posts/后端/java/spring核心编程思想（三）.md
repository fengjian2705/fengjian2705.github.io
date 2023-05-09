---
title: Spring 核心编程思想（三）：Spring IoC 容器概述
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/05/06/debcba54d0f154b7.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. Spring IoC 容器概述

| 内容                    |
| ----------------------- |
| Spring IoC 依赖查找     |
| Spring IoC 依赖注入     |
| Spring IoC 依赖来源     |
| Spring IoC 配置元信息   |
| Spring IoC 容器         |
| Spring 应用上下文       |
| 使用 Spring IoC 容器    |
| Spring IoC 容器生命周期 |
| 面试题精选              |

## 2. Spring IoC 依赖查找

1. 根据 Bean 名称查找

   * 实时查找

   * 延迟查找

2. 根据 Bean 类型查找
   * 单个 Bean 对象
   * 集合 Bean 对象

3. 根据 Bean 名称 + 类型查找
4. 根据 Java 注解查找
   * 单个 Bean 对象
   * 集合 Bean 对象

## 3. 依赖查找实践

### 3.1 根据 Bean 名称实时查找

1. 新建实体类 User

   ```java
   package tech.fengjian.thinking.in.spring.ioc.overview.domain;
   
   /**
    * 用户类
    *
    * @author 风间
    * @since 2023/5/7 12:58
    */
   public class User {
   
       private Long id;
       private String name;
   
       public Long getId() {
           return id;
       }
   
       public void setId(Long id) {
           this.id = id;
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   '}';
       }
   }
   ```

2. 新建 dependency-lookup-context 文件，配置实体类

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaIoCation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <bean id="user" class="tech.fengjian.thinking.in.spring.ioc.overview.domain.User">
           <property name="id" value="1"/>
           <property name="name" value="jack"/>
       </bean>
   
   </beans>
   ```
   
3. 编写测试类 DependencyLookupDemo.java

   ```java
   package tech.fengjian.thinking.in.spring.ioc.overview.dependency.lookup;
   
   import org.springframework.beans.factory.BeanFactory;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   import tech.fengjian.thinking.in.spring.ioc.overview.domain.User;
   
   /**
    * 依赖查找示例
    *
    * @author 风间
    * @since 2023/5/7 12:51
    */
   public class DependencyLookupDemo {
       public static void main(String[] args) {
           // 配置 XML 文件
           // 启动 Spring 应用上下文
           BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-lookup-context.xml");
           User user = (User) beanFactory.getBean("user");
           System.out.println("user = " + user);
       }
   }
   
   ```

### 3.2  根据 Bean 名称延迟查找

1. 面试题：ObjectFactory、BeanFactory、FactoryBean 的区别？

   答：`ObjectFactory`、`BeanFactory` 和 `FactoryBean` 是 Spring 框架中几个常用的工厂类，它们的主要区别如下：

   * `ObjectFactory` 是一个简单的对象创建工厂，用于实现对象的延迟加载，只有在调用 `getObject()` 方法时才会创建目标对象。

   * `BeanFactory` 是 Spring IoC 容器的根接口，提供了管理 bean 的机制并支持依赖注入等功能。它可以读取配置文件，并创建和管理 bean 实例。`BeanFactory` 是最基本的容器，其他的容

   器都是对它的扩展。

   * `FactoryBean` 是一个特殊的 bean，它实现了 `FactoryBean` 接口，并通过 `getObject()` 方法创建和管理其他 bean 实例。它可以用来实现一些复杂的 bean 构建逻辑，也可以用来添加一

   些 AOP 切面处理或提供一些自定义的对象包装等功能。需要注意的是，虽然这三个工厂类都与对象的创建和管理有关，但它们的作用与用法略有不同，不能直接混淆使用。总体来说，`ObjectFactory` 

   主要用于实现延迟加载，`BeanFactory` 是 Spring IoC 容器的基础，用于管理和创建 bean，而 `FactoryBean` 则是一个特殊的 bean，用于实现一些特殊的创建逻辑。

2. 延迟查找就利用到了`ObjectFactory`，它有一个 FactoryBean 的实现 `ObjectFactoryCreatingFactoryBean`,`ObjectFactoryCreatingFactoryBean` 与 `ObjectFactory` 之间存

   在一定关系，它们都是在实现 Spring 中的延迟初始化时可以使用的工具类。具体来说，`ObjectFactoryCreatingFactoryBean` 是 Spring 中的一个工厂 bean 实例，它实现了 `FactoryBean` 

   接口，可以用于创建 `ObjectFactory` 对象。`ObjectFactory` 本身是 Spring 中的一个接口，可以用于实现对象的延迟初始化。当 `ObjectFactoryCreatingFactoryBean` 被注入到其他 bean 

   中时，Spring IoC 容器会先创建 `ObjectFactoryCreatingFactoryBean` 实例，并调用其 `getObject()` 方法，该方法会创建一个 `ObjectFactory` 对象，并返回该对象实例。当我们需要使

   用被延迟初始化的 bean 时，我们可以从该 `ObjectFactory` 实例中获取目标 bean 的实例进行使用。因此，可以使用 `ObjectFactoryCreatingFactoryBean` 和 `ObjectFactory` 来实现在 

   Spring 中的延迟初始化。其中，`ObjectFactoryCreatingFactoryBean` 用于创建 `ObjectFactory` 实例，而 `ObjectFactory` 则用于实现对象的延迟初始化。

3. dependency-lookup-context.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaIoCation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <bean id="user" class="tech.fengjian.thinking.in.spring.ioc.overview.domain.User">
           <property name="id" value="1"/>
           <property name="name" value="jack"/>
       </bean>
   
       <bean id="objectFactory" class="org.springframework.beans.factory.config.ObjectFactoryCreatingFactoryBean">
           <property name="targetBeanName" value="user"/>
       </bean>
   
   </beans>
   
   ```
   
4. 测试类，lookupInLazy

   ```java
   package tech.fengjian.thinking.in.spring.ioc.overview.dependency.lookup;
   
   import org.springframework.beans.factory.BeanFactory;
   import org.springframework.beans.factory.ObjectFactory;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   import tech.fengjian.thinking.in.spring.ioc.overview.domain.User;
   
   /**
    * 依赖查找示例
    * 1. 通过名称的方式来查找
    *
    * @author 风间
    * @since 2023/5/7 12:51
    */
   public class DependencyLookupDemo {
       public static void main(String[] args) {
           // 配置 XML 文件
           // 启动 Spring 应用上下文
           BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-lookup-context.xml");
           lookupInLazy(beanFactory);
       }
   
       private static void lookupInLazy(BeanFactory beanFactory) {
           ObjectFactory<User> objectFacoty = (ObjectFactory<User>) beanFactory.getBean("objectFacoty");
           User user = objectFacoty.getObject();
           System.out.println("延迟查找：" + user);
       }
   
   }
   ```

### 3.3 根据 Bean 类型查找单个对象

```java
package tech.fengjian.thinking.in.spring.ioc.overview.dependency.lookup;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.thinking.in.spring.ioc.overview.domain.User;

/**
 * 依赖查找示例
 * 1. 通过名称的方式来查找
 *
 * @author 风间
 * @since 2023/5/7 12:51
 */
public class DependencyLookupDemo {
    public static void main(String[] args) {
        // 配置 XML 文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-lookup-context.xml");
        // 按照类型查找单个对象
        lookupByType(beanFactory);
    }

    private static void lookupByType(BeanFactory beanFactory) {
        User user = beanFactory.getBean(User.class);
        System.out.println("实时查找："+user);
    }

}

```

### 3.4 根据 Bean 类型查找集合 Bean 对象

ListableBeanFactory

```java
package tech.fengjian.thinking.in.spring.ioc.overview.dependency.lookup;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.ListableBeanFactory;
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.thinking.in.spring.ioc.overview.domain.User;

import java.util.Map;

/**
 * 依赖查找示例
 * 1. 通过名称的方式来查找
 * 2. 通过类型的方式来查找
 *
 * @author 风间
 * @since 2023/5/7 12:51
 */
public class DependencyLookupDemo {
    public static void main(String[] args) {
        // 配置 XML 文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-lookup-context.xml");
        // 按照类型查找集合对象
        lookupCollectionByType(beanFactory);
       
    }

    private static void lookupCollectionByType(BeanFactory beanFactory) {
        if (beanFactory instanceof ListableBeanFactory) {
            ListableBeanFactory listableBeanFactory = (ListableBeanFactory) beanFactory;
            Map<String, User> users = listableBeanFactory.getBeansOfType(User.class);
            System.out.println("查找到所有的 User 集合对象：" + users);
        }
    }
}

```

### 3.5 根据 Bean 名称 + 类型查找

```java
User user = beanFactory.getBean("user",User.class);
```

### 3.6 根据 Java 注解查找 Bean 对象

1. 编写注解 @Super

```java
package tech.fengjian.thinking.in.spring.ioc.overview.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface Super {
}

```

2. 新增实体类 SuperUser 继承 User

```java
package tech.fengjian.thinking.in.spring.ioc.overview.domain;

import tech.fengjian.thinking.in.spring.ioc.overview.annotation.Super;

@Super
public class SuperUser extends User{

    private String address;

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "SuperUser{" +
                "address='" + address + '\'' +
                "} " + super.toString();
    }
}

```

3. Dependency-lookup-context.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaIoCation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="tech.fengjian.thinking.in.spring.ioc.overview.domain.User">
        <property name="id" value="1"/>
        <property name="name" value="jack"/>
    </bean>

    <bean id="superUser" class="tech.fengjian.thinking.in.spring.ioc.overview.domain.SuperUser" parent="user"
    primary="true">
        <property name="address" value="苏州"/>
    </bean>

    <bean id="objectFactory" class="org.springframework.beans.factory.config.ObjectFactoryCreatingFactoryBean">
        <property name="targetBeanName" value="user"/>
    </bean>

</beans>

```

3. 编写测试代码

```java
package tech.fengjian.thinking.in.spring.ioc.overview.dependency.lookup;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.ListableBeanFactory;
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.thinking.in.spring.ioc.overview.annotation.Super;
import tech.fengjian.thinking.in.spring.ioc.overview.domain.User;

import java.util.Map;

/**
 * 依赖查找示例
 * 1. 通过名称的方式来查找
 * 2. 通过类型的方式来查找
 *
 * @author 风间
 * @since 2023/5/7 12:51
 */
public class DependencyLookupDemo {
    public static void main(String[] args) {
        // 配置 XML 文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-lookup-context.xml");
        // 通过注解查找对象
        lookupByAnnotationType(beanFactory);

    }

    private static void lookupByAnnotationType(BeanFactory beanFactory) {
        if (beanFactory instanceof ListableBeanFactory) {
            ListableBeanFactory listableBeanFactory = (ListableBeanFactory) beanFactory;
            Map<String, Object> users = listableBeanFactory.getBeansWithAnnotation(Super.class);
            System.out.println("查找标注 @Super 所有 User 集合对象：" + users);
        }
    }
}

```

## 4. 依赖注入实践

* 根据 Bean 名称注入
* 根据 Bean 类型注入
  * 单个 Bean 对象
  * 集合 Bean 对象
* 注入容器内建 Bean 对象
* 注入非 Bean 对象
* 注入类型
  * 实时注入
  * 延迟注入

### 4.1 手动配置依赖注入

1. 新建 UserRepository

```java
package tech.fengjian.ioc.container.overview.repository;

import tech.fengjian.ioc.container.overview.domain.User;

import java.util.Collection;

public class UserRepository {

    private Collection<User> users;

    public Collection<User> getUsers() {
        return users;
    }

    public void setUsers(Collection<User> users) {
        this.users = users;
    }
}
```

2. 新建 dependency-injection-context.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaIoCation="
        http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">

    <bean id="user" class="tech.fengjian.ioc.container.overview.domain.User">
        <property name="id" value="1"/>
        <property name="name" value="jack"/>
        <property name="age" value="18"/>
    </bean>

    <bean id="superUser" class="tech.fengjian.ioc.container.overview.domain.SuperUser">
        <property name="address" value="苏州"/>
    </bean>

    <bean id="userRepository" class="tech.fengjian.ioc.container.overview.repository.UserRepository">
        <property name="users">
            <util:list>
                <ref bean="user"/>
                <ref bean="superUser"/>
            </util:list>
        </property>
    </bean>
</beans>
```

**tips:**`<util:list>`使用需要引入`xmlns:util="http://www.springframework.org/schema/util`约束头

3. 编写测试代码

```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.ioc.container.overview.repository.UserRepository;

/**
* <h1>依赖注入示例</h1>
* @author 风间
* @since 2023/5/8
*/
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        UserRepository userRepository = (UserRepository)beanFactory.getBean("userRepository");
        System.out.println(userRepository.getUsers());

    }
}

```

### 4.2 自动注入依赖

autowire

修改 dependency-injection-context.xml 文件

```xml
<bean id="userRepository" class="tech.fengjian.ioc.container.overview.repository.UserRepository" autowire="byType"/>
```

### 4.3 注入非 Bean 对象

比较内建的 BeanFactory 和容器 BeanFactory 的异同

1. 定义 BeanFactory 属性

```java
package tech.fengjian.ioc.container.overview.repository;

import org.springframework.beans.factory.BeanFactory;

public class UserRepository {

    private BeanFactory beanFactory;
    
    public void setBeanFactory(BeanFactory beanFactory) {
    	this.beanFactory = beanFactory;
    }
    public BeanFactory getBeanFactory() {
        return beanFactory;
    }
}
```

2. 编写测试代码

```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.ioc.container.overview.repository.UserRepository;

/**
* <h1>依赖注入示例</h1>
* @author 风间
* @since 2023/5/8
*/
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        UserRepository userRepository = (UserRepository)beanFactory.getBean("userRepository");
        System.out.println(userRepository.getBeanFactory());
        System.out.println(userRepository.getBeanFactory() == beanFactory);
    }
}

```

3. 结果分析：

```shell
org.springframework.beans.factory.support.DefaultListableBeanFactory@22f71333: defining beans [user,superUser,userRepository]; root of factory hierarchy
false
```

这里我们在 UserRepository 中定义的 BeanFactory 是内建 Bean 对象

我们看下 BeanFactory 是哪里来的？

* 从容器中获取一下，依赖查找

```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
* <h1>依赖注入示例</h1>
* @author 风间
* @since 2023/5/8
*/
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        BeanFactory bean = beanFactory.getBean(BeanFactory.class);

    }
}
```

输出异常信息：没有这个 Bean 的定义

```java
Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'org.springframework.beans.factory.BeanFactory' available
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:351)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:342)
	at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1126)
	at tech.fengjian.ioc.container.overview.dependency.injection.DependencyInjectionDemo.main(DependencyInjectionDemo.java:18)

```

### 4.4 实时注入和延迟注入

ObjectFactory

```java
package tech.fengjian.ioc.container.overview.repository;

import org.springframework.beans.factory.ObjectFactory;

public class UserRepository {

    private ObjectFactory<User> objectFactory;

    public ObjectFactory<User> getObjectFactory() {
        return objectFactory;
    }

    public void setObjectFactory(ObjectFactory<User> objectFactory) {
        this.objectFactory = objectFactory;
    }
}

```

编写测试代码：

```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.ioc.container.overview.domain.User;
import tech.fengjian.ioc.container.overview.repository.UserRepository;

/**
* <h1>依赖注入示例</h1>
* @author 风间
* @since 2023/5/8
*/
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        UserRepository userRepository = (UserRepository) beanFactory.getBean("userRepository");
        ObjectFactory<User> objectFactory = userRepository.getObjectFactory();
        System.out.println(objectFactory.getObject());
    }
}
```

修改一下 UserRepository 中的 ObjectFactory 中的实体类型

```java
package tech.fengjian.ioc.container.overview.repository;

import org.springframework.beans.factory.ObjectFactory;
import org.springframework.context.ApplicationContext;

import java.util.Collection;

public class UserRepository {

    private ObjectFactory<ApplicationContext> objectFactory;

    public ObjectFactory<ApplicationContext> getObjectFactory() {
        return objectFactory;
    }

    public void setObjectFactory(ObjectFactory<ApplicationContext> objectFactory) {
        this.objectFactory = objectFactory;
    }
}

```

再比较下 BeanFactory 和 ObjectFactory 中的 Bean 对象

```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.ioc.container.overview.repository.UserRepository;

/**
 * <h1>依赖注入示例</h1>
 *
 * @author 风间
 * @since 2023/5/8
 */
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        UserRepository userRepository = (UserRepository) beanFactory.getBean("userRepository");
        System.out.println(userRepository.getObjectFactory().getObject() == beanFactory);
    }
}

```

结果为 true

 ## 5. 依赖注入来源

* 自定义 Bean
* 容器內建 Bean 对象
* 容器內建依赖

```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.core.env.Environment;
import tech.fengjian.ioc.container.overview.repository.UserRepository;

/**
 * <h1>依赖注入示例</h1>
 *
 * @author 风间
 * @since 2023/5/8
 */
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        // 依赖来源一：自定义 Bean
        UserRepository userRepository = (UserRepository) beanFactory.getBean("userRepository");
        // 依赖来源二：依赖注入（内建依赖）
        System.out.println(userRepository.getBeanFactory());
        // 依赖来源三：容器内建 Bean
        Environment environment = beanFactory.getBean(Environment.class);
        System.out.println("获取 Environment 类型的 Bean：" + environment);
    }
}
```

## 6. 配置元信息

* Bean 定义配置
  * 基于 XML 文件
    基于 Properties 文件
    基于 Java 注解
    基于 JavaAPI (专题讨论)
* IoC 容器配置
  * 基于 XML 文件
  * 基于 Java 注解
  * 基于 JavaAPI (专题讨论)
* 外部化属性配置
  * 基于 Java 注解(@Value...)

## 7. IoC 容器之谜

BeanFactory 和 ApplicationContext 谁才是 IoC 容器？

```java
package tech.fengjian.ioc.container.overview.repository;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.ObjectFactory;
import tech.fengjian.ioc.container.overview.domain.User;

import java.util.Collection;

public class UserRepository {

 
    private BeanFactory beanFactory;// 内建非 Bean 对象
  
    public void setBeanFactory(BeanFactory beanFactory) {
        this.beanFactory = beanFactory;
    }

    public BeanFactory getBeanFactory() {
        return beanFactory;
    }
}

```



```java
package tech.fengjian.ioc.container.overview.dependency.injection;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.ioc.container.overview.repository.UserRepository;

/**
* <h1>依赖注入示例</h1>
* @author 风间
* @since 2023/5/8
*/
public class DependencyInjectionDemo {

    public static void main(String[] args) {

        // 配置 XML 配置文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
        UserRepository userRepository = (UserRepository)beanFactory.getBean("userRepository");
        System.out.println(userRepository.getBeanFactory());
        System.out.println(userRepository.getBeanFactory() == beanFactory);
    }
}
```

为什么 `userRepository.getBeanFactory() == beanFactory`不成立？

简单来看他们是两个对象，当然不同。

官方文档描述：

The [`BeanFactory`](https://docs.spring.io/spring-framework/docs/5.3.28-SNAPSHOT/javadoc-api/org/springframework/beans/factory/BeanFactory.html) interface provides an advanced configuration mechanism capable of managing any type of object. [`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.28-SNAPSHOT/javadoc-api/org/springframework/context/ApplicationContext.html) is a sub-interface of `BeanFactory`.It adds:

- Easier integration with Spring’s AOP features
- Message resource handling (for use in internationalization)
- Event publication
- Application-layer specific contexts such as the `WebApplicationContext` for use in web applications.

In short, the `BeanFactory` provides the configuration framework and basic functionality, and the `ApplicationContext` adds more enterprise-specific functionality. The `ApplicationContext` is a complete superset of the `BeanFactory` and is used exclusively in this chapter in descriptions of Spring’s IoC container. For more information on using the `BeanFactory` instead of the `ApplicationContext,` see the section covering the [`BeanFactory` API](https://docs.spring.io/spring-framework/docs/5.3.28-SNAPSHOT/reference/html/core.html#beans-beanfactory)

译：`BeanFactory` 接口提供了一个高级配置机制，能够管理任何类型的对象。`ApplicationContext` 是 `BeanFactory` 的子接口。`ApplicationContext` 增加了：

* 更易于集成 Spring 的 AOP 特性

* 消息资源处理（用于国际化）

* 事件发布

* 应用程序层特定的上下文，例如 WebApplicationContext 用于 Web 应用程序。

简而言之，BeanFactory 提供配置框架和基本功能，ApplicationContext 添加了更多企业特定功能。ApplicationContext 是 BeanFactory 的完整超集，在 Spring 的 IoC 容器描述中专门使用。有关使用 BeanFactory 而不是 ApplicationContext 的详细信息，请参阅涵盖 BeanFactory API 的部分。

他说是管理是对象，他说并没有说管理是 Bean，依赖来源并不只是限于 Bean，所以他对象是描述得非常精确的。

再来看看我们编写的测试代码：

```java
BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
```

我们为什么这么写，`ClassPathXmlApplicationContext`就是`ApplicationContext`,`ApplicationContext` is `BeanFactory`，这里也可以定义如下：

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:META-INF/dependency-injection-context.xml");
```

那也就是说其实比较的是 userRepository.getBeanFactory() 和 ApplicationContext对象，为什么不等于呢，这里的 ApplicationContext 他是一种设计模式：

ClassPathXmlApplicationContext <- AbstractXmlApplicationContext <- AbstractRefreshableConfigApplicationContext <- AbstractRefreshableApplicationContext <-

AbstractApplicationContext <- `ConfigurableApplicationContext`

**tips：**<- 代表继承

ConfigurableXxx 是一种可写的方式，他有 getBeanFactory 方法：

```java
ConfigurableListableBeanFactory getBeanFactory() throws IllegalStateException;
```

`ConfigurableApplicationContext` <- `ApplicationContext` <- `BeanFactory`，也就是 ConfigurableApplicationContext 就是 BeanFactory 了，为什么还多此一举的提供一个

getBeanFactory()方法来返回一个 BeanFactory 呢？

我们来进一步看一下 getBeanFactory() 方法的内部实现，AbstractRefreshableApplicationContext 类中：

```java
/** Bean factory for this context. */
@Nullable
private DefaultListableBeanFactory beanFactory;

@Override
public final ConfigurableListableBeanFactory getBeanFactory() {
    synchronized (this.beanFactoryMonitor) {
        if (this.beanFactory == null) {
            throw new IllegalStateException("BeanFactory not initialized or already closed - " +
                    "call 'refresh' before accessing beans via the ApplicationContext");
        }
        return this.beanFactory;
    }
}
```

这个 BeanFactory 其实是一个组合的，就相当于说我这个代码其实是把这个 BeanFactory 的实现 DefaultListableBeanFactory 组合进来了，并不是完全的抽象继承了父类。

也就是在上下文里面的实现它是组合了一个方式，同时在接口上又是继承的关系，这种方式有点像代理，再看一下 getBean() 方法的实现，在 AbstractApplicationContext 类中：

```java
@Override
public <T> T getBean(Class<T> requiredType) throws BeansException {
    assertBeanFactoryActive();
    return getBeanFactory().getBean(requiredType);
}
```

先 getBeanFactory() 获取 BeanFactory 对象，然后去 getBean()，相当于用代理对象（组合对象）去获取这个东西。
也就是说 BeanFactory 其实是一个**底层的 IoC 容器**，ApplicationContext 是在这基础上增加了一些他的特性。	

## 8. Spring 应用上下文

ApplicationContext 除了 IoC 容器角色,还有提供:

* 面向切面(AOP)
* 配置元信息(Configuration Metadata)
* 资源管理(Resources)
* 事件(Events)
* 国际化(i18n)
* 注解(Annotations)
* Environment抽象(Environment Abstraction)

## 9. 使用 IoC 容器

* BeanFactory 是 Spring 底层 IoC 容器
* ApplicationContext 是具备应用特性的 BeanFactory 超集

### 9.1 XML 场景下底层 IoC 容器：BeanFactory

```java
	package tech.fengjian.ioc.container.overview.container;

import org.springframework.beans.factory.support.DefaultListableBeanFactory;
import org.springframework.beans.factory.xml.XmlBeanDefinitionReader;

/**
* <h1>{@link BeanFactory} 作为 IoC 容器示例</h1>
* @author 风间
* @since 2023/5/9
*/
public class BeanFactoryAsIoCContainerDemo {

    public static void main(String[] args) {

        // 创建 BeanFactory 容器
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
        XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);
        // XML 配置文件 classpath 路径
        String IoCation = "META-INF/dependency-injection-context.xml";
        // 加载配置，返回加载到的 Bean 个数
        int loadBeanDefinitions = reader.loadBeanDefinitions(IoCation);
        System.out.println(loadBeanDefinitions);

    }
}
```

解析：DefaultListableBeanFactory 是 IoC 的底层容器，XmlBeanDefinitionReader 是 XML 方式的 Bean 定义的一个读取器，观察他的构造方法：需要一个 BeanDefinitionRegistry 接口类型

的参数，DefaultListableBeanFactory 这个类呢又实现了 BeanDefinitionRegistry 接口，所以可以利用 XmlBeanDefinitionReader 和 DefaultListableBeanFactory 来加载 Bean 的配置。

一句话可以总结为：从 IoCation 的位置把配置好的 Bean 加载到 DefaultListableBeanFactory 定义的 IoC 容器中去。

```java
public XmlBeanDefinitionReader(BeanDefinitionRegistry registry) {
		super(registry);
	}
```

### 9.2 注解场景下 ApplicationContext 作为 IoC 容器

```java
package tech.fengjian.ioc.container.overview.container;

import org.springframework.beans.factory.ListableBeanFactory;
import org.springframework.beans.factory.support.DefaultListableBeanFactory;
import org.springframework.beans.factory.xml.XmlBeanDefinitionReader;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import tech.fengjian.ioc.container.overview.domain.User;

import java.util.Map;

/**
 * <h1>{@link org.springframework.beans.factory.BeanFactory}作为 IoC 容器示例</h1>
 *
 * @author 风间
 * @since 2023/5/9
 */
public class AnnotationApplicationContextAsIoCContainerDemo {

    public static void main(String[] args) {

        // 创建 ApplicationContext 容器
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        // 将当前类 AnnotationApplicationContextAsIoCContainerDemo 作为配置类（Configuration Class）
        applicationContext.register(AnnotationApplicationContextAsIoCContainerDemo.class);
        // 启动应用上下文
        applicationContext.refresh();
        // 依赖查找集合对象
        lookupCollectionByType(applicationContext);
    }

    @Bean
    public User user() {
        User user = new User();
        user.setId(1l);
        user.setName("jack");
        user.setAge(18);
        return user;
    }

    private static void lookupCollectionByType(AnnotationConfigApplicationContext beanFactory) {
        if (beanFactory instanceof ListableBeanFactory) {
            ListableBeanFactory listableBeanFactory = (ListableBeanFactory) beanFactory;
            Map<String, User> users = listableBeanFactory.getBeansOfType(User.class);
            System.out.println("查找到的所有 User 集合对象：" + users);
        }
    }

}

```

## 10. IoC 容器生命周期

### 启动

我们先看下 applicationContext.refresh() 方法，字面意思是重新刷新，实际意义呢是启动。

```java
@Override
public void refresh() throws BeansException, IllegalStateException {
    synchronized (this.startupShutdownMonitor) {
        // Prepare this context for refreshing.
        prepareRefresh();

        // Tell the subclass to refresh the internal bean factory.
        ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

        // Prepare the bean factory for use in this context.
        prepareBeanFactory(beanFactory);

        try {
            // Allows post-processing of the bean factory in context subclasses.
            postProcessBeanFactory(beanFactory);

            // Invoke factory processors registered as beans in the context.
            invokeBeanFactoryPostProcessors(beanFactory);

            // Register bean processors that intercept bean creation.
            registerBeanPostProcessors(beanFactory);

            // Initialize message source for this context.
            initMessageSource();

            // Initialize event multicaster for this context.
            initApplicationEventMulticaster();

            // Initialize other special beans in specific context subclasses.
            onRefresh();

            // Check for listener beans and register them.
            registerListeners();

            // Instantiate all remaining (non-lazy-init) singletons.
            finishBeanFactoryInitialization(beanFactory);

            // Last step: publish corresponding event.
            finishRefresh();
        }

        catch (BeansException ex) {
            if (logger.isWarnEnabled()) {
                logger.warn("Exception encountered during context initialization - " +
                        "cancelling refresh attempt: " + ex);
            }

            // Destroy already created singletons to avoid dangling resources.
            destroyBeans();

            // Reset 'active' flag.
            cancelRefresh(ex);

            // Propagate exception to caller.
            throw ex;
        }

        finally {
            // Reset common introspection caches in Spring's core, since we
            // might not ever need metadata for singleton beans anymore...
            resetCommonCaches();
        }
    }
}
```

* `synchronized (this.startupShutdownMonitor)`：首先他加了一把锁，这个锁为什么要加，因为任何一个 ApplicationContext 可以在任意的代码里去进行创建，那也可以多线程创建，Spring 的设计者也不知道你是不是处于一个线程安全还是非安全的情况。
* `prepareRefresh`:准备刷新，做一些前期准备工作,
  * 记录启动时间
  * 初始化 PropertySources 
  * 和校验有关的一些东西 getEnvironment().validateRequiredProperties()
  * earlyApplicationListeners

```java
protected void prepareRefresh() {
		// Switch to active.
		this.startupDate = System.currentTimeMillis();
		this.closed.set(false);
		this.active.set(true);

		if (logger.isDebugEnabled()) {
			if (logger.isTraceEnabled()) {
				logger.trace("Refreshing " + this);
			}
			else {
				logger.debug("Refreshing " + getDisplayName());
			}
		}

		// Initialize any placeholder property sources in the context environment.
		initPropertySources();

		// Validate that all properties marked as required are resolvable:
		// see ConfigurablePropertyResolver#setRequiredProperties
		getEnvironment().validateRequiredProperties();

		// Store pre-refresh ApplicationListeners...
		if (this.earlyApplicationListeners == null) {
			this.earlyApplicationListeners = new LinkedHashSet<>(this.applicationListeners);
		}
		else {
			// Reset IoCal application listeners to pre-refresh state.
			this.applicationListeners.clear();
			this.applicationListeners.addAll(this.earlyApplicationListeners);
		}

		// Allow for the collection of early ApplicationEvents,
		// to be published once the multicaster is available...
		this.earlyApplicationEvents = new LinkedHashSet<>();
	}
```

* obtainFreshBeanFactory：用于创建一个新的、空的 `BeanFactory`，要使用此方法必须先调用 `refresh()` 方法刷新应用程序上下文。该方法将创建一个基本的 `DefaultListableBeanFactory` 实例，如果需要可以自定义实现

```java
protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
		refreshBeanFactory();
		return getBeanFactory();
	}
```

​	看下 `refreshBeanFactory` 方法，在 `AbstractRefreshableApplicationContext` 类中，创建了 `DefaultListableBeanFactory`对象

```java
@Override
protected final void refreshBeanFactory() throws BeansException {
    if (hasBeanFactory()) {
        destroyBeans();
        closeBeanFactory();
    }
    try {
        DefaultListableBeanFactory beanFactory = createBeanFactory();
        beanFactory.setSerializationId(getId());
        customizeBeanFactory(beanFactory);
        loadBeanDefinitions(beanFactory);
        synchronized (this.beanFactoryMonitor) {
            this.beanFactory = beanFactory;
        }
    }
    catch (IOException ex) {
        throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
    }
}

```

* `prepareBeanFactory(beanFactory)` 方法，一大段操作

```java
protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
		// Tell the internal bean factory to use the context's class loader etc.
		beanFactory.setBeanClassLoader(getClassLoader());
		beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
		beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));

		// Configure the bean factory with context callbacks.
		beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
		beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
		beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
		beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
		beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);

		// BeanFactory interface not registered as resolvable type in a plain factory.
		// MessageSource registered (and found for autowiring) as a bean.
		beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
		beanFactory.registerResolvableDependency(ResourceLoader.class, this);
		beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
		beanFactory.registerResolvableDependency(ApplicationContext.class, this);

		// Register early post-processor for detecting inner beans as ApplicationListeners.
		beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));

		// Detect a LoadTimeWeaver and prepare for weaving, if found.
		if (beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
			beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));
			// Set a temporary ClassLoader for type matching.
			beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
		}

		// Register default environment beans.
		if (!beanFactory.containsIoCalBean(ENVIRONMENT_BEAN_NAME)) {
			beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());
		}
		if (!beanFactory.containsIoCalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
			beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
		}
		if (!beanFactory.containsIoCalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
			beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
		}
	}
```

我们说`Environment`对象为什么能够通过依赖查找的方式可以查得到，就是受到这段 registerSingleton 代码的注入进去，为什么我们可以得到`BeanFactory`这个对象，因为这里通过registerResolvableDependency 可以把 BeanFactory 作为一个非 Bean 的方式来进行注入

* postProcessBeanFactory、invokeBeanFactoryPostProcessors：进一步初始化动作，这里相当于交给你去做，相当于 BeanFactory 扩展点，制定你的 Factory 来实现

* registerBeanPostProcessors：对 Bean 进行调整
* initMessageSource：国际化
* initApplicationEventMulticaster：事件广播
* registerListeners：注册监听器
* finishBeanFactoryInitialization：初始化完成 

总结一下 ApplicationContext 启动过程中的一些基本操作：

第一个：创建 BeanFactory -> 进行初步的初始化 -> 加入一些内建的 Bean 对象或者 Bean 依赖以及加上一些内建的非 Bean 依赖

第二个：BeanFactory的扩展点，通过执行 BeanFactoryPostProcessor，你可以定义成 Bean，可以添加到容器

第三个：对 Bean 的一些修改或者扩展，通过执行 BeanPostProcessor，这里只是注册，具体的调用是在 BeanFactory 中进行操作的

接下来就会进行国际化、事件注册，那资源操作在哪里呢，ResourceLoader

### 停止

applicationContext.close() 方法

```java
@Override
public void close() {
    synchronized (this.startupShutdownMonitor) {
        doClose();
        // If we registered a JVM shutdown hook, we don't need it anymore now:
        // We've already explicitly closed the context.
        if (this.shutdownHook != null) {
            try {
                Runtime.getRuntime().removeShutdownHook(this.shutdownHook);
            }
            catch (IllegalStateException ex) {
                // ignore - VM is already shutting down
            }
        }
    }
}
```

再看 doClose 方法

```java
protected void doClose() {
    // Check whether an actual close attempt is necessary...
    if (this.active.get() && this.closed.compareAndSet(false, true)) {
        if (logger.isDebugEnabled()) {
            logger.debug("Closing " + this);
        }

        LiveBeansView.unregisterApplicationContext(this);

        try {
            // Publish shutdown event.
            publishEvent(new ContextClosedEvent(this));
        }
        catch (Throwable ex) {
            logger.warn("Exception thrown from ApplicationListener handling ContextClosedEvent", ex);
        }

        // Stop all Lifecycle beans, to avoid delays during individual destruction.
        if (this.lifecycleProcessor != null) {
            try {
                this.lifecycleProcessor.onClose();
            }
            catch (Throwable ex) {
                logger.warn("Exception thrown from LifecycleProcessor on context close", ex);
            }
        }

        // Destroy all cached singletons in the context's BeanFactory.
        destroyBeans();

        // Close the state of this context itself.
        closeBeanFactory();

        // Let subclasses do some final clean-up if they wish...
        onClose();

        // Reset IoCal application listeners to pre-refresh state.
        if (this.earlyApplicationListeners != null) {
            this.applicationListeners.clear();
            this.applicationListeners.addAll(this.earlyApplicationListeners);
        }

        // Switch to inactive.
        this.active.set(false);
    }
}
```

销毁 Bean，关闭 BeanFactory，还可以进一步的自定义操作 onClose() 做一些动作。

## 11. 面试题

**<font color="green" size="2">沙雕面试题</font>**-什么是 Spring IoC 容器？

答：Spring Framework implementation of the Inversion of Control (IoC) principle. IoCis also known as dependency injection (DI). It is a process whereby 

objects define their dependencies (that is, the other objects they work with) only through constructor arguments, arguments to a factory imethod,or properties 

that are set on the object instance after it is constructed or returned from a factory method. The containerthen injects those dependencies when it creates 

the bean.

Spring Framework 是一种实现控制反转（IoC）原则的框架，IoC 也被称为依赖注入（DI）。它是一种过程，其中对象通过构造函数参数、工厂方法的参数或在构造对象后或从工厂方法返回后设置在对象实例上

的属性来定义其依赖项（即其与其他对象的配合工作）。容器然后在创建 Bean 时注入这些依赖项。

**<font color="orange" size="2">996面试题</font>**-BeanFactory 和 FactoryBean 的区别？

答：BeanFactory是IoC底层容器 FactoryBean 是创建 Bean 的一种方式,帮助实现复杂的初始化逻辑，看下代码说明：

Interface to be implemented by objects used within a BeanFactory which are themselves factories for individual objects. If a bean implements this interface,it

 is used as a factory for an object to expose, not directly as a bean instance that will be exposed itself.

这是一个接口去实现一个 Object，然后这个 Object 中有几个特性，这个特性其实就是为了帮助你去暴露一个 Bean，他这个 Bean 呢通常来说不是一个正常的 Bean，或者说不是一个能够简单处理的 Bean。

我们看下之前的 User 对象，他是非常简单的 POJO,他就是一个默认的构造器参数通过反射的方式进行调用。但是现实情况可能没这么简单，假设 User 这个对象是第三方来进行创建的，这个时候咋办呢，没办法

通过反射的方式直接去获取这个对象进行初始化，因此你可以通过 BeanFactory 的方式来进行操作：

getObject 是主要逻辑，这个方法会被容器调用，这个容器怎么知道这个方法要被调用呢，这个前提就是 getObjectType，这个 getObjectType 去决定哪个对象要去做，如果对象的类型相同怎么办，就通过

是不是单例（isSingleton）的方式来进行区分，如果每次去获取的时候都是同一个对象的话，如果 isSingleton 是 true（默认），他返回的是同一个对象，否则的话呢就不是同一个对象。

那 getObject 是不是每次都会被调用，答案是否定的。

也就是说 FactoryBean 是解决复杂场景下 Bean 的初始化或者构造问题，那么创建的 Bean 是不是还会经过 Bean 的生命周期呢？后续揭秘

**<font color="red" size="2">劝退面试题</font>**-Spring IoC 容器启动时做了哪些准备?？

答：IoC 配置元信息读取和解析、IoC 容器生命周期、Spring 事件发布、国际化等,更多答案将在后续专题章节逐一讨论



__本节完__
