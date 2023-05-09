---
title: Spring 核心编程思想（四）：Spring Bean 基础
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/04/27/4b24c59fe6e1571c.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. Spring Bean 基础

| 内容                   |
| ---------------------- |
| 定义 Spring Bean       |
| BeanDefinition 元信息  |
| 命名 Spring Bean       |
| Spring Bean 的别名     |
| 注册 Spring Bean       |
| 实例化 Spring Bean     |
| 初始化 Spring Bean     |
| 延迟初始化 Spring Bean |
| 销毁 Spring Bean       |
| 垃圾回收 Spring Bean   |
| 面试题精选             |

## 2. 定义 Spring Bean

* 什么是 BeanDefinition?
* BeanDefinition 是 Spring Framework 中定义 Bean 的配置元信息接口,包含:
  * Bean 的类名
  * Bean 行为配置元素,如作用域、自动绑定的模式、生命周期回调等
  * 其他 Bean 引用,又可称作合作者(Collaborators)或者依赖(Dependencies)
  * 配置设置,比如 Bean 属性(Properties)

## 3. BeanDefinition 元信息

1. BeanDefinition 元信息

| 属性                     | 说明                                            |
| ------------------------ | ----------------------------------------------- |
| Class                    | Bean 全类名，必须是具体类，不能用抽象类或接口   |
| Name                     | Bean 的名称或者 ID                              |
| Scope                    | Bean 的作用域（如：singleton、prototype 等）    |
| Constructor arguments    | Bean 构造器参数（用于依赖注入）                 |
| Properties               | Bean 属性设置（用于依赖注入）                   |
| Autowiring mode          | Bean 自动绑定模式（如：通过名称 byName）        |
| Lazy initialization mode | Bean 延迟初始化模式（延迟和非延迟），默认非延迟 |
| Initialization method    | Bean 初始化回调方法名称                         |
| Destruction method       | Bean 销毁回调方法名称                           |

2. BeanDefinition 构建

   * 通过 BeanDefinitionBuilder

   * 通过 AbstractBeanDefinition 以及派生类

```java
package thinking.in.spring.spring.bean.definition;

import org.springframework.beans.MutablePropertyValues;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.GenericBeanDefinition;
import tech.fengjian.ioc.container.overview.domain.User;

/**
 * <h1>{@link org.springframework.beans.factory.config.BeanDefinition}构建示例</h1>
 *
 * @author 风间
 * @since 2023/5/9
 */
public class BeanDefinitionCreationDemo {

    public static void main(String[] args) {

        // 1. 通过 BeanDefinitionBuilder 构建
        BeanDefinitionBuilder beanDefinitionBuilder = BeanDefinitionBuilder.genericBeanDefinition(User.class);
        // 通过属性删除
        // beanDefinitionBuilder.addPropertyValue("id", 1l);
        // beanDefinitionBuilder.addPropertyValue("name", "rose");
        // beanDefinitionBuilder.addPropertyValue("age", 16);
        beanDefinitionBuilder
                .addPropertyValue("id", 1l)
                .addPropertyValue("name", "rose")
                .addPropertyValue("age", 16);
        // 获取 BeanDefinition 实例
        BeanDefinition beanDefinition = beanDefinitionBuilder.getBeanDefinition();
        // BeanDefinition 并非 Bean 的终态，可以自定义修改

        // 2. 通过 AbstractBeanDefinition 以及派生类
        // beanDefinitionBuilder.getBeanDefinition()  返回的其实是一个 AbstractBeanDefinition
        GenericBeanDefinition genericBeanDefinition = new GenericBeanDefinition();
        // 设置 Bean 的类型
        genericBeanDefinition.setBeanClass(User.class);
        // 通过 MutablePropertyValues 批量操作属性，beanDefinitionBuilder 底层也是如此 this.beanDefinition.getPropertyValues().add(name, value);
        MutablePropertyValues propertyValues = new MutablePropertyValues();
        propertyValues.addPropertyValue("id", 2);
        propertyValues.addPropertyValue("name", "jack");
        propertyValues.addPropertyValue("age", 18);
        genericBeanDefinition.setPropertyValues(propertyValues);

    }
}
```

## 4. 命名 Spring Bean

* Bean 的名称

  每个 Bean 拥有一个或多个标识符(identifiers),这些标识符在 Bean 所在的容器必须是唯一的。通常,一个 Bean 仅有一个标识符,如果需要额外的,可考虑使用别名(Alias)来扩充。

  在基于XML的配置元信息中,开发人员可用 id 或者 name 属性来规定 Bean 的标识符。通常 Bean 的标识符由字母组成,允许出现特殊字符。如果要想引入 Bean 的别名的话,可在 name 属性使用半角逗号

  (",")或分号(";")来间隔。Bean 的 id 或 name 属性并非必须制定,如果留空的话,容器会为Bean自动生成一个唯一的名称。Bean 的命名尽管没有限制,不过官方建议采用驼峰的方式,更符合 Java 的命

  名约定。

**tips：**每个 Bean 它的识别符是在它所在的容器，也就说它所在的 BeanDefinition 里面或者说 BeanFactory 里面是唯一的并非是整个应用是唯一的这个地方是要加以区别的。









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
