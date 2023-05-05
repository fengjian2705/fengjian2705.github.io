---
title: Spring 核心编程思想（一）：Spring Framework 总览
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/05/05/55a1ab8e31eb229d.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. Spring Framework 总览



| 项目                       | 说明                                                      |
| -------------------------- | --------------------------------------------------------- |
| 1.  准备工作               | 学习 Spring Framework 需要哪些前期铺垫                    |
| 2.  特性总览               | 了解 Spring Framework 提供了哪些功能特性                  |
| 3.  版本特性               | 关于版本分布与特性之间的关系                              |
| 4.  模块化设计             | Spring 如何从单一的模块划分成不同的模块                   |
| 5.  对 Java 语言特性的运用 | Spring 每个版本的语言支持                                 |
| 6.  对 JDK API 实践        | Spring 对 Java API 的整合和实践                           |
| 7.  对 Java EE API 的整合  | Spring 对 Java EE 的整合，比如 Servlet 规范、JPA 规范等等 |
| 8.  编程模型               | Spring 是如何实践和实现 Java 编程模型的一个示范           |
| 9.  核心价值               | Spring 对我们日常开发和一些架构学习中带来哪些重要特性     |
| 10. 面试题精选             | -                                                         |

## 2. 准备工作

* **心态**

​	戒骄戒躁 谨慎豁达 如履薄冰

* **方法**

​	基础：夯实基础，了解动态

​	思考：保持怀疑，验证一切

​	分析：不拘小节，观其大意

​	实践：思辨结合，学以致用

* **工具**

​	JDK: Oracle JDK 8

​	Spring Framwork：5.2.2.RELEASE

​	IDE: InteliJ IDEA 2022

​	MAVEN: 3.2+

## 3. 特性总览

### 3.1 核心特性（Core）

* IoC 容器(loCContainer)

* Spring 事件(Events)

* 资源管理(Resources)

* 国际化(i18n)

* 校验(Validation)

* 数据绑定(DataBinding)

* 类型转换(TypeConversion)

* Spring 表达式(Spring Express Language)

* 面向切面编程(AOP)

### 3.2 数据存储（Data Access）

* JDBC

* 事务抽象(Transactions)

* DAO 支持(DAOSupport)

* O/R 映射 (O/R Mapping)

* XML编列(XML Marshalling) 

### 3.3 Web技术(Web)

* Web Servlet 技术栈

​	Spring MVC

​	WebSocket

​	SockJS

* Web Reactive 技术栈

​	Spring WebFlux

​	WebClient

​	WebSocket

### 3.4 技术整合(Integration)

* 远程调用(Remoting)

* Java 消息服务(JMS)

* Java 连接架构(JCA)

* Java 管理扩展(JMX)

* Java 邮件客户端(Email)

* 本地任务(Tasks)

* 本地调度(Scheduling)

* 缓存抽象(Caching)

* Spring 测试(Testing)

  模拟对象(Mock Objects)

  TestContext 框架(TestContext Framework)

  SpringMVC 测试(SpringMVCTest)

  Web 测试客户端(WebTestClient)

## 4. 版本特性

Java 版本依赖与支持

| Spring Framework版本 | Java标准版 | Java企业版            |
| -------------------- | ---------- | --------------------- |
| 1.x                  | 1.3+       | J2EE 1.3 +            |
| 2.x                  | 1.4.2+     | J2EE 1.3 +            |
| 3.x                  | 5+         | J2EE 1.4 和 Java EE 5 |
| 4.x                  | 6+         | Java EE 6 和 7        |
| 5.x                  | 8+         | Java EE 7             |

**tips：**

* Java5 之前的 Java 的标准版叫 J2SE，就是 Java2 然后 Standard E这个版本。
* Java5 之前的 Java 的企业版叫 J2EE。

### 1.x 版本

1. 为什么从 1.3 开始？

   因为Spring Framework的早期的版本叫什么，interface21，那么这个版本其实就依赖于 Java1.3，Java1.3 引入了一个非常重要的特性是什么，就是动态代理。

   从 Java1.3 开始就会针对接口的方式来进行动态代理，那么这是实现 AOP 的一个很重要的环节，因此 Spring 的第 1 个版本就必须依赖 Java1.3。

2. 那么与此同时它支持的 JavaEE 版本是 1.3，这个版本一个简单特性就是 Servlet 的 API 对应的 JavaEE 的版本是 1.3，Servlet 的版本是 2.3 这个版本，2.3 这个版本它在里面会支持 Servlet 事件，那么因此它可以和我们的 Spring的事件来进行一个呼应，他们都是 Java 标准事件的实现。

### 2.x 版本

这个版本里面主要是支持了一些包括我们常见的比如说NIO的支持，那么这时候JavaEE的企业版本并没有做太多的更新，它还是支持到了JavaEEJ2EE的1.3这个版本

### 3.x 版本

3.x 其实是比较重大的版本，Spring 从 3 这个版本开始引入到大量的注解，所以它需要的支持的版本是 Java5，因为我们知道 Java5 里面会提升到一些注解，包括注解、枚举这样的东西，所以在 3 里面会引入它大量的注解和枚举。所以这个时候版本的要求最低的Java标准版的要求是Java5。

那么对应的 JavaEE 的版本，这里是指的是支持的版本，从 J2EE 1.4到 Java EE 5，那么这个版本就这么一个过渡过程。

那么 3 为什么非常重要，因为3基本上确定了 Spring Framework 的一个内核，这个内核是比较多的，包括比如说注解驱动、事件驱动，包括一些AOP的支持，它都是这个版本做的比较完善。

### 4.x 版本和 5.x 版本	

那么4这个版本基本上在3的版本上面增加了一些新的东西，那么主要是一些细节性的东西，包括注解上面的提升，包括利用 Java8 里面的API来进行提升，那么当然它这个4版本并不要求一定是 Java8，那么它最低要求是 Java6 就可以了。那么这是为了照顾到更多的人去使用到 Spring Framework 4这个版本。

那么与此同时从 SpringFramework 4 开始也是对于 Spring Boot 1.x的一个支持，那么1.x就是说 Spring Boot 的 1 版本，也就说 Spring Boot的 1.x 版本它是基于Spring4来进行开发的，那么 SpringBoot2 是基于 Spring Framework 5 来开发的。所以你会看到它的一个区别点，那么从5开始对应的是Java EE 7。

其实这个时候从 Spring4 开始，其实 Spring 的翅膀就非常硬了，它对 JavaEE 的支持其实是一种若即若离的那种感觉，所以因此基本上从 4 开始形成了自己的体系，尤其 Spring Boot 起来之后，包括Spring Cloud 出现之后，基本上它的完整的体系就已经生成了。

## 5. 模块化设计

Spring 大概分了 20 个模块：

spring-aop：面向接口编程

spring-aspects：Spring 对 aspects 的支持

spring-jms：JMS 其实是 Java Message Service 的一个缩写，比如说Java的一个消息服务，那么这里可以对应的比如说 Apache 的 ActiveMQ 或者其他传统的 JavaEE 的消息中间件，那么这部分内容只针对于说我们的 JMS 规范来进行实施的，那么因此它会利用到大量的 JMS 的 API 来进行实现	

spring-context-indexer

spring-context-support

spring-context:

spring-beans：spring-beans和spring-context合成起来就是Spring IoC的一个重要的核心的实现，无论是 spring-beans 还是 spring-context，都是通过 spring-core 来进行支持的

spring-core：spring-core就包含了一些关于 Java 语法的特性的支持以及林林总总

spring-messaging：Spring想统一一下消息服务的一个实现，那么包括了我们说的JMS包括了Kafka，包括 RocketMQ 或者是 RabbitMQ，它都会有一个统一的实现的一个标准，那么这个东西也和JMS也是一样的，JMS 过去是希望通过一套标准的 API 来统一比如说 MQ 或者是比如说 WebLogicMQ 或者是 WebSphereMQ 的一个实现，那么 Spring 它的野心更大，它希望通过它自己 API 来帮助大家实现最简单或者是最好用的一个API的体验

spring-orm：就是我们比如说 Hibernate、JPA 这种东西的一个进行整合

spring-oxm：就是我们前面讲的XML编列，就是 marshal 和 unmarshal 这么一个东西，也就说这是XML里面的序列化和反序列化，这个东西是一个新的模块，Spring进行单独的一个维护

spring-test：这个测试包含了我们前面讲的 Mock 对象，包括我们说的 TestContext，比如说测试上下文，还包括 Spring 的 MVC 的测试，以及WebClient的测试，Web客户端的测试

spring-tx：tx其实是transaction的缩写，就是我们常讲的就是我们说Spring的事务抽象，这部分其实对于大家来说是非常重点，它是基本上借鉴了 JDBC 的一部分一个事务实现以及 Java EE 尤其是 EJB的一个事务实现，做一个统一的封装

spring-web：

spring-webflux：

spring-webmvc：

spring-websocket：

spring-expression：Spring的表达式语言，从 Spring 3 开始进行引入的，那么它类似于像 JSP 里面的 EL 语言

spring-instrument：这是 Spring 2开始之时对我们的Java的装配，简单讲就是 Java 的 agent 的一个支持

spring-jcl：那么这个模块是从 Spring 5开始支持的，因为我们知道我们过去运用过另外一个模块，就是关于 commons-logging，commons-logging 是统一了 Java 的 logging，就是 Java 的日志和Log4j 这个日志，那么 Java logging 之后又出现了一个 Loqback，Logback它是个新型的一个日志框架，那么又用到了 SLF4J，SLF4j 就相当于说又把 Java logging、我们的Log4j和我们的 Logback 来进行统一，那么Spring为了解决这个情况,它自己用一套新型的日志框架,那么就是我们说的JCL这个框架,那么这个框架会帮助Spring来统一它的日志管理。

spring-jdbc：是Spring对JDBC的一个整合

## 6. 对 Java 语言特性的运用



![](https://s3.bmp.ovh/imgs/2023/05/05/46524e0cf73a3fbe.png)

### Java5 语法特性

| 语法特性             | Spring支持版本 | 代表实现                   |
| -------------------- | -------------- | -------------------------- |
| 注解(Annotation)     | 1.2 +          | @Transactional             |
| 枚举(Enumeration)    | 1.2 +          | Propagation                |
| for-each语法         | 3.0+           | AbstractApplicationContext |
| 自动装箱(AutoBoxing) | 3.0+           |                            |
| 泛型(Generic)        | 3.0+           | ApplicationListener        |

### Java6 语法特性

| 语法特性       | Spring支持版本 | 代表实现 |
| -------------- | -------------- | -------- |
| 接口 @Override | 4.0 +          |          |

### Java7 语法特性

| 语法特性                | Spring支持版本 | 代表实现                    |
| ----------------------- | -------------- | --------------------------- |
| Diamond 语法            | 5.0 +          | DefaultListableBeanFactory  |
| try-with-resources 语法 | 5.0 +          | ResourceBundleMessageSource |

### Java8 语法特性

| 语法特性   | Spring支持版本 | 代表实现                      |
| ---------- | -------------- | ----------------------------- |
| Lambda语法 | 5.0 +          | PropertyEditorRegistrySupport |

## 7. 对 JDK API 实践

| JDK 版本 | 核心 API                                                     |
| -------- | ------------------------------------------------------------ |
| < Java5  | 反射(Reflection)<br/>Java Beans<br/>动态代理(Dynamic Proxy)  |
| Java5    | 并发框架(J.U.C)<br/>格式化(Formatter)<br/>Java管理扩展(JMX)<br/>Instrumentation<br/>XML处理(DOM、SAX、XPath、<br/>XSTL) |
| Java6    | JDBC 4.0 (JSR 221)<br/>JAXB 2.0 (JSR 222)<br/>可插拔注解处理API(JSR269)<br/>Common Annotations (JSR 250)<br/>Java Compiler API (JSR 199)<br/>Scripting in JVM (JSR 223) |
| Java7    | NIO 2 (JSR 203)<br/>Fork/Join框架(JSR 166)<br/>invokedynamic字节码(JSR 292) |
| Java8    | Stream API (JSR 335)<br/>CompletableFuture (J.U.C)<br/>Annotation on Java Types (JSR 308)<br/>Date and Time API (JSR 310)<br/>可重复Annotations(JSR 337)<br/>JavaScript运行时(JSR 223) |
| Java9    | Reactive Streams Flow API (J.U.C)<br/>Process API Updates (JEP 102)<br/>Variable Handles (JEP 193)<br/>Method Handles (JEP 277)<br/>Spin-Wait Hints (JEP 285)<br/>Stack-Walking API (JEP 259) |

**tips: **JSR 是Java Specification Request
