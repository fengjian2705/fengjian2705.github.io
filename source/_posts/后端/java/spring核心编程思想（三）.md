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
   * 根据 Java 注解查找
   * 单个 Bean 对象
   * 集合 Bean 对象

## 3. 依赖查找

### 3.1 根据 Bean 名称实时查找

1. 新建实体类 User.java

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

2. 新建 dependency-lookup-context.xml 文件，配置实体类

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
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
          xsi:schemaLocation="http://www.springframework.org/schema/beans
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

### 3.6 根据 Java 注解查找单个 Bean 对象

1. 编写注解 Super

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
       xsi:schemaLocation="http://www.springframework.org/schema/beans
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

### 3.7 根据 Java 注解查找集合 Bean 对象

