---
title: Spring 核心编程思想（四）：Spring 依赖查找
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/04/27/4b24c59fe6e1571c.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. Spring Bean 基础

| 内容                 |
| -------------------- |
| 依赖查找的今世前生   |
| 单一类型依赖查找     |
| 集合类型依赖查找     |
| 层次性依赖查找       |
| 延迟依赖查找         |
| 安全依赖查找         |
| 内建可查找的依赖     |
| 依赖查找中的经典异常 |
| 面试题精选           |



## 12. 面试题

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
