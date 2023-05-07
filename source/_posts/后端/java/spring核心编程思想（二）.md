---
title: Spring 核心编程思想（二）：重新认识 IoC
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/05/06/43c27790d5a8ea71.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. 重新认识 IoC

| 内容                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| IoC 发展简介             | 包括 IoC 的定义以及它的一个简史                              |
| IoC 主要实现策略         | 其实 IoC 不只是我们所看到的包括 Martin Fowler 或者是像 Spring 官方的它的一个讨论 |
| IoC 容器的职责           | 有多方解读来进行说明                                         |
| IoC 容器的实现           | 包括了我们的开源实现和传统实现                               |
| 传统 IoC 容器实现        | 着重介绍就是一种关于Java Beans 对 IoC 的容器的一个实现       |
| 轻量级 IoC 容器          | 如何定义以及它给我们带来的好处，有什么值得我们学习的地方     |
| 依赖查找 VS 依赖注入     | 为什么我们说Spring框架里面会偏好于依赖注入而非依赖查找       |
| 构造器注入 VS Setter注入 | 关于构造器注入和 Setter 方法注入的一个区别和优势             |
| 面试题精选               | -                                                            |

## 2. IoC 发展简介

### 2.1 什么是 IoC?

In software engineering, inversion of control (IoC) is a programmiing principle. IoC inverts the flow of control as compared to traditional control flow. In IoC, custom-wvritten portions of a computer program receive the flow of control from a generic framework. Asoftware architecture with this design inverts

control as compared to traditional procedural programming: in traaditional programming, the custom code that expresses the purpose of the program calls into 

reusable libraries to take care of generic tasks, but with inversion of control, it is the framework that calls into the custom,or task-specific,code.

在软件工程中，控制反转（IoC）是一种编程原则。与传统的控制流相比，IoC 反转了控制流。在 IoC 中，计算机程序的自定义部分从通用框架接收控制流。具有这种设计的软件架构与传统的过程式编程相比，控

制被反转了：在传统的编程中，表达程序目的的自定义代码调用可重用库来处理通用任务，但是在控制反转中，是框架调用自定义或特定任务的代码。



​                                                                                                       来源:https://en.wikipedia.org/wiki/Inversion_of_control

### 2.2 发展简介

1983 年,Richard E. Sweet 在《The Mesa Programming Environment》中提出"Hollywood Principle"(好莱坞原则)

1988 年,Ralph E.Johnson&Brian Foote在《Designing ReusableClasses》中提出"Inversion of control"(控制反转)

1996 年,Michael Mattson 在《Object-Oriented Frameworks, Asurvey of methodological issues》中将 "Inversion of control" 命名为 "Hollywood principle"

<font color="red">2004 年,Martin Fowler 在《Inversion of Control Containers andthe Dependency Injectionpattern》中提出了自己对 IoC 以及 DI 的理解</font>

2005 年,Martin Fowler 在《Inversion of Control》对 IoC 做出进一步的说明

## 3. IoC 主要实现策略

* 维基百科(https://en.wikipedia.org/wiki/Inversion_of_control)

  Implementation techniques小节的定义:

  "In object-oriented programming, there are several basic techniquees to implement inversion of control. These are:

  * Using a service IoCator pattern：服务定位模式，这种模式是 JavaEE 里面所定义的一种模式，通常通过 JNDI 这种技术获取 JavaEE 的组件，比如说获取 EJB 组件或者 DataSource 这样的东西
  * Using dependency injection, for example：依赖注入
    * Constructor injection：构造器注入
    * Parameter injection：参数注入
    * Setter injection：set 方法注入
    * Interface injection：接口注入
  * Using a contextualized lookup：上下文依赖查询，由另外一种技术来进行实现，比如说在 Java 里面有 Java Beans 这样的技术，Java Beans 里面有一个通用的上下文叫做 bean context，这种里面既可以传输我的 Bean 也可以来管理我的Bean的层次性
  * Using template method design pattern：关于模板方法的一个设计模式，这种设计模式在Spring里面大量地会用到，比如说是SpringJDBC里面会用到，JDBC Template这样的实现会给我们一种类似于比如说Statement这样的Callback这种Callback能够帮助我们实现地更为抽象，当我们去实现这样的接口的时候我们不需要关心Callback从哪来，那么也实现了一种反转控制的方式
  * Using strategy design pattern:策略模式"

* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>》提到的主要实现策略:

  "IoC is a broad concept that can be implemented in different ways. There are two main types:

  **Dependency Lookup（依赖查找）**: The container provides callbacks to components, and a lookup context. This is

  the EJB and Apache Avalon approach. It leaves the onus each component to use container APls

  look up resources and collaborators. The Inversion of Control is limited to the container invoking

  callback methods that application code can use to obtain resources.

  **Dependency Injection（依赖注入）**: Components do no look up; they provide plain Java methods enabling the

  container to resolve dependencies. The container is wholly responsible for wiring up components,

  passing resolved objects in to JavaBean properties or constructors. Use of JavaBean properties is

  called Setter Injection; use of constructor arguments is called Constructor Injection."

## 4. IoC 容器的职责

1. 维基百科(https://en.wikipedia.org/wiki/Inversion_of_control)

在Overview小节中提到:

"Inversion of control serves the following design purposes:

* To decouple（解耦） the execution of a task from implementation.
* To focus a module on the task it is designed for.
* To free modules from assumptions about how other systems do what they do and instead rely on
  contracts.
* To prevent side effects when replacing a module.

Inversion of control is sometimes facetiously referred to as the 'Hollywood Principle: Don't call us, we'll
call you'."

* 通用职责
* 依赖处理
  * 依赖查找
  * 依赖注入
* 生命周期管理
  * 容器
  * 托管的资源(JavaBeans 或其他资源)
* 配置
  * 容器
  * 外部化配置
  * 托管的资源(JavaBeans 或其他资源)

## 5. IoC 容器的实现

* 主要实现
  * Java SE
    * Java Beans
    * Java ServiceLoader SPl
    * JNDI (Java Naming and Directory Interface)
  * Java EE
    * EJB (Enterprise Java Beans)
    * Servlet
  * 开源
    * Apache Avalon (http://avalon.apache.org/closed.html)
    * PicoContainer (http://picocontainer.com/)
    * Google Guice (https://github.com/google/guice)
    * Spring Framework (https://spring.io/projects/spring-framework)

## 6. 传统 IoC 容器实现：Java Beans

* 特性
  * 依赖查找
  * 生命周期管理
  * 配置元信息
  * 事件
  * 自定义
  * 资源管理
  * 持久化
* 规范
  * JavaBeans: https://www.oracle.com/technetwork/java/javase/tech/index-jsp-138795.html
  * BeanContext: https://docs.oracle.com/javase/8/docs/technotes/guides/beans/spec/beancontext.htm

* Java Beans 实战

  通常来说对于 Java Bean 的理解可以认为是一个简单的 POJO，但是对 Java Bean 的理解可以了解得更多

  * 新建 maven 项目 java-beans-demo

  * 新建 Person 类 

    ```java
    package tech.fengjian.ioc.java.beans;
    
    /**
     * 描述人的 POJO 类
     * <p>
     * POJO: Setter/Getter方法
     * Java Beans：可写方法(Writable)/可读方法(Readable)
     */
    public class Person {
    
        private String name;// Property
        private Integer age;
    
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
    }
    ```

    

  * 新建 BeanInfoDemo 类

    ```java
    package tech.fengjian.ioc.java.beans;
    
    import java.beans.BeanInfo;
    import java.beans.IntrospectionException;
    import java.beans.Introspector;
    import java.util.Arrays;
    
    /**
     * {@link java.beans.BeanInfo} 示例
     */
    public class BeanInfoDemo {
    
        public static void main(String[] args) throws IntrospectionException {
    
            // 通过 Java Beans 自省操作定义 BeanInfo
            BeanInfo beanInfo = Introspector.getBeanInfo(Person.class);
    
            Arrays.stream(beanInfo.getPropertyDescriptors()).forEach(propertyDescriptor -> {
                System.out.println(propertyDescriptor);
            });
        }
    }
    ```

  * 关于 BeanInfo 的解析

    ```java
    package java.beans;
    
    import java.awt.Image;
    
    public interface BeanInfo {
    
        BeanDescriptor getBeanDescriptor();
    
        EventSetDescriptor[] getEventSetDescriptors();
    
        int getDefaultEventIndex();
    
        PropertyDescriptor[] getPropertyDescriptors();
    
        int getDefaultPropertyIndex();
    
        MethodDescriptor[] getMethodDescriptors();
    
        BeanInfo[] getAdditionalBeanInfo();
    
        Image getIcon(int iconKind);
    
    }
    
    ```

    这个类里会有一些描述：

    * BeanDescriptor getBeanDescriptor();

      Bean 的 Descriptor，那么 Bean 的 Descriptor 这里主要是指的一些Bean的基本的描述

    * EventSetDescriptor[] getEventSetDescriptors();

      关于事件上的描述，那么这事件就包括我的事件的一个处理的方法

    * PropertyDescriptor[] getPropertyDescriptors();

      还有就是关于我的 Property 描述器或者描述符，描述我们定义的写方法和读方法

  * 上面 BeanInfoDemo 输出结果：

    ```java
    java.beans.PropertyDescriptor[name=age; propertyType=class java.lang.Integer; readMethod=public java.lang.Integer tech.fengjian.ioc.java.beans.Person.getAge(); writeMethod=public void tech.fengjian.ioc.java.beans.Person.setAge(java.lang.Integer)]
    java.beans.PropertyDescriptor[name=class; propertyType=class java.lang.Class; readMethod=public final native java.lang.Class java.lang.Object.getClass()]
    java.beans.PropertyDescriptor[name=name; propertyType=class java.lang.String; readMethod=public java.lang.String tech.fengjian.ioc.java.beans.Person.getName(); writeMethod=public void tech.fengjian.ioc.java.beans.Person.setName(java.lang.String)]
    ```

    输出了 age、class、name 三个 Property 描述信息。这里多出了一个 class，这个是 Object 类中的 getClass 方法，跟定义 BeanInfo 的方法参数有关，若指定 stopClass 为 Object有，那么 class 就不会输出

    ```java
    BeanInfo beanInfo = Introspector.getBeanInfo(Person.class,Object.class);
    ```

## 7. 轻量级 IoC 容器

* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>》认为轻量级容器的特征:

  A container that can manage application code.能够管理到我的应用代码，并不是说我们是个代码的托管工具，它是说我们的容器可以管理代码运行，比如说可以控制代码的一个启停。

  A container that is quick to start up.说它能够快速地启动。

  A container that doesn't require any special deployment steps to deploy objects within it.它不需要一些特殊的配置

  A container that has such a light footprint and minimal API dependencies that it can be run in a variety of environments.容器它能够达到一些比较轻量级的内存占用,以及最小化的 API 的一个依赖

  A container that sets the bar for adding a managed object so low in terms of deployment effort and performance. overhead that it's possible to deploy and

  manage fine-grained objects, as well as coarse-grained components.

  容器就需要一些可以管控的这么一个渠道去部署和管理一些细粒度的对象甚至是一些粗粒度的组件，主要它是要达到一些部署上的一个效率以及相关的性能上面的一个开销，那么是它对相当于容器的一个补充说明。

* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>》认为轻量级容器的好处：

  Escaping the monolithic container 释放掉一些容器，就所谓的聚式或者是单体这样的容器，monolithic 这种单词就是聚式或者说我们的单体应用

  Maximizing codereusability 要实现最大化的一个代码的复用

  Greater object orientation 更大程度上面的面向对象

  Greater productivity 更大化的一个产品化

  Better testability 更好的可测试性



## 8. 依赖查找 VS 依赖注入

从 5 个纬度来进行对比：没有绝对的好与坏，只有相对的合理

| 类型     | 依赖处理 | 实现便利性 | 代码侵入性   | API 依赖性     | 可读性 |
| -------- | -------- | ---------- | ------------ | -------------- | ------ |
| 依赖查找 | 主动获取 | 相对繁琐   | 侵入业务逻辑 | 依赖容器 API   | 良好   |
| 依赖注入 | 被动提供 | 相对便利   | 低侵入性     | 不依赖容器 API | 一般   |

## 9. 构造器注入 VS Setter注入

* Spring Framework 对构造器注入与 Setter 的论点:

  "The Spring team generally <font color="red">advocates constructor injection</font>, as it lets you implement application components as immutable objects and ensures that required 

  dependencies are not null. Furthermore, constructor-injected components are always returned to the client (calling) code in a fully initialized state. As a side note, a large number of constructor arguments a bad code smell, implying that the class likely has too many responsibilities and should be refactored

  to better address proper separation of concerns.

  Spring 团队通常倡导使用构造函数注入（constructor injection），因为它可以让你把应用组件实现为不可变对象，并确保必需的依赖项不为空。此外，构造函数注入的组件总是以完全初始化的状态返回给客户端代码（调用方）。一个额外的说明是，大量的构造函数参数是一种不好的代码气味，意味着这个类可能有太多的职责，应该进行重构，以更好地实现关注点分离。

  <font color="red"> Setter injection should primarily only be used for optional dependencies</font> that can be assigned reasonable default values within 

  the class. Otherwise, not-null checks must be performed everywhere the code uses the dependency. One benefit of setter injection is that setter methods

  make objects of that class amenable to reconfiguration or re-injection later Management through JMX MBeans is therefore a compelling use case for setter

  injection."

  Setter 注入应主要仅用于可以在类内分配合理默认值的可选依赖项。否则，在代码使用该依赖项的所有地方都必须执行非空检查。 Setter 注入的一个好处是，setter 方法使得那个类的对象容易在以后进行重新配置或重新注入。因此，通过JMX MBeans进行管理是 Setter 注入的一个很好的用例。

* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>)认为 Setter 注入的优点:

  "<font color="red">Advantages of Setter Injection</font> include:

  * JavaBean properties are <font color="red">well supported</font> in IDES.

  * JavaBean properties are <font color="red">self-documenting</font>.

  * JavaBean properties are inherited by subclasses without the need for any code

  * It's possible to use <font color="red">the standard JavaBeans property-editor machinery for type conversions</font> if necessary

  * Many existing JavaBeans can be used within a JavaBean-oriented IoC container without modification

  * If there is a corresponding getter for each setter (making the property readable, as well as writable), it is possible to ask

  the component for its current configuration state. This is particularly useful if we want to persist that state: for example,

  in an XML form or in a database. With Constructor Injection, there's no way to find the current state.

  * Setter Injection works well for objects that have default values, meaning that not all properties need to be supplied at

  runtime."

  Setter 注入的优点包括：

  * JavaBean 属性在 IDE 中获得很好的支持。

  * JavaBean 属性是自我记录的。

  * JavaBean 属性被子类继承，无需任何代码。

  * 如有必要，可以使用标准的 JavaBeans 属性编辑器机制进行类型转换。

  * 许多现有的 JavaBeans 可以在 JavaBean 导向的 IoC 容器中使用而无需修改。

  * 如果每个 setter 都有相应的 getter（使属性可读和可写），则可以询问组件其当前配置状态。如果想要保存该状态，这非常有用，例如在XML表单或数据库中。使用构造函数注入，没有办法找到当前状态。

  * Setter 注入适用于具有默认值的对象，这意味着不需要在运行时提供所有属性。

* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>》认为 Setter 注入的缺点:

  "<font color="red">Disadvantages</font> include:

  The order in which setters are called is not expressed in any contract. Thus, we sometimes need to invoke a method after the last setter has been called to initialize the component. Spring provides the `org.springframework.beans.factory.InitializingBean` interface for this; it also provides the ability to invoke an arbitrary init method. However, this contract must be documented to ensure correct use outside a container. Not all the necessary setters may have been called before use. The object can thus be left partially configured."

  缺点包括：

  在任何契约中都没有表达调用 setter 的顺序。因此，我们有时需要在最后一个 setter 被调用之后调用方法来初始化组件。Spring 提供了`org.springframework.beans.factory.InitializingBean` 接口和调用任意初始化方法的能力。然而，这个契约必须被记录以确保在容器之外正确使用。在使用之前可能没有调用所有必要的setter，因此对象可能会部分配置不完整。

* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>》认为构造器注入的优点:
  "<font color="red">Advantages of Constructor Injection</font> include:
  * Each managed object is guaranteed to be in a consistent state-fully configured-before it can be invoked in any
    business methods. This is the primary motivation of Constructtor Injection. (However, it is possible to achieve the
    same result with JavaBeans via dependency checking, as Spring can optionally perform.) There's no need for
    initialization methods.
  * There may be slightly less code than results from the useof multiple JavaBean methods, although will be no
    difference in complexity."
* 《Expert One-on-One<sup>TM</sup> J2EE<sup>TM</sup> Development without EJB<sup>TM</sup>)认为构造器注入的缺点:
  "<font color="red">Disadvantages</font> include:
  * Although also a Java-language feature, multi-argument coonstructors are probably less common in existing code thause
    of JavaBean properties.
  * Java constructor arguments don't have names visible by introspection
  * Constructor argument lists are less well supported by IDEs tharJavaBean setter methods.
  * Long constructor argument lists and large constructor bodies can become unwieldy
  * Concrete inheritance can become problematic.
  * Poor support for optional properties, compared to JavaBeaans
  * Unittesting can be slightly more difficult
  * When collaborators are passed in on object construction, it becomes impossible to change the reference held in the
    object."

## 10. 面试题

**<font color="green" size="2">沙雕面试题</font>**-什么是 IoC？

答:

简单地说,IoC 是反转控制,类似于好莱坞原则,主要有依赖查找和依赖注入实现，

进一步说的话：按照IoC的定义很多方面都是IoC,我们常说的比如说 JavaBeans 是 IoC 的一个容器实现、Servlet 的容器也是 IoC 的实现，因为 Servlet 可以去依赖或者反向地通过 JNDI 的方式进行得到一些外部的一些资源包括 DataSource 或者相关的 EJB 的组件，与此同时像是 Spring Framework 或者 PicoContainer 的依赖注入的框架也能够帮助我们去实现我们的IoC。同时除此之外这些东西是我们比较常见的一个 IoC 的实现策略。按照它这个定义如果是反转控制那东西就多了去了，包括我们说消息其实也算，因为消息其实是被动的我们如果说我们传统的调用链路是一个主动拉的模式那么 IoC 其实是一种推的模式，那么推的模式在消息事件以及各种这样类似于这种反向的观察者模式的扩展都属于 IoC 那么这东西就无穷无尽了，那么它如果仅仅关注于依赖注入比如说通过构造器注入或 Setter 注入，那么它其中有什么好处。

**<font color="orange" size="2">996面试题</font>**-依赖查找和依赖注入的区别？

答:依赖查找是主动或手动的依赖查找方式,通常需要依赖容器或标准 API 实现。而依赖注入则是手动或自动依赖绑定的方式,无需依赖特定的容器和 API。

**<font color="red" size="2">劝退面试题</font>**-Spring 作为 IoC 容器有什么优势？

答:
典型的 IoC 管理,依赖查找和依赖注入

AOP 抽象

事务抽象

事件机制

SPI 扩展

强大的第三方整合

易测试性

更好的面向对象
