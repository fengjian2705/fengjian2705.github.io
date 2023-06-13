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

## 1. 内容提要

## ![](https://s3.bmp.ovh/imgs/2023/06/13/5e426061fe2c133f.png)



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

| Spring Framework 版本 | Java 标准版 | Java 企业版           |
| --------------------- | ----------- | --------------------- |
| 1.x                   | 1.3+        | J2EE 1.3 +            |
| 2.x                   | 1.4.2+      | J2EE 1.3 +            |
| 3.x                   | 5+          | J2EE 1.4 和 Java EE 5 |
| 4.x                   | 6+          | Java EE 6 和 7        |
| 5.x                   | 8+          | Java EE 7             |

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

* 2004 是 Spring Framework 发布的年份，所以 Spring 在支持的时候，他在支持第一个版本的时候，他只要支持到 Java 的 1.3，不需要到 1.5，但他要考虑到 1.5 的支持，比如后面我们讲的 Spring 1.2 的版本的时候就开始支持了，比如我们常说的 Java 管理扩展，就是 Java Management Extensions，那么这个版本里面的分布就包括这么一些特性，包括枚举、泛型、注解、封箱或拆箱这么一些特性

* 那么 Java6 其实没有在 Java5 的基础上面做很多支持，只允许在接口上面增加 @Override，这个注解其实是强制要求子类或者子接口要覆盖父类或者父接口里面那个方法，那么过去在类里面是可以打的，那么@Override我们也知道它是新的一个注解的方式，在Java 5里面被引用到了，但是在接口上面是从Java6开始支持的。这部分特性其实在Spring里面体现的并不是非常的多，同时我们用的时候基本上也感知不到

* 对 Java7 主要有两大特点，第1个是 Diamond 语法，Diamond 语法简单是这么个意思，就是在我们用集合的时候我们要用到泛型类型，比如说一个 List，它的集合的元素类型的是 String，那么在newArrayList 的时候。就 new 数组的实现的时候，String 括号里面的东西可写可不写。不写的时候就是Diamond语法，写的时候就非Diamond语法。

  还有一个就是多 Catch，多 Catch 新语法特性就是当你的一个 Exception 就是一个代码在执行的时候可能会遇到多个异常，那么这时候可以用一句话把多个异常来重新捕获那么这个东西在Spring的实现里面也有体现。

  再来就是关于 Try resource，那么我们前面提到的东西一个专业术语叫 ARM（Automatic Resource Management），比如说我们在关闭 IO 的时候我们要调用一个 close 方法，那么通过try-with-resources 语法之后，可以不用强制去调 IO，其实这只是个语法层面的一个变化，底层还是会动态字节码生成一个close方法来调用

* Java8一个非常显著的特点是支持在Lambda语法以及可重复注解，我们通常来说一个注解只能在一个类或一个方法上面标注一次，那么从 Java8 开始可以一个类或一个方法上面可以标注多个注解，那么这就是所谓的可重复注解。

  还有一个就是类型注解，类型注解是个新的一个注解的方式，那么在Spring里面体现的并不是太多，从Java 9和Java 10开始，Spring Framework 5里面并没有提供Java 9和Java10之后的语法的或者API的支持，因为他考虑到Java9由于模块化的实现之后，其实Java社区产生了一定的分裂，就是说还是保留在Java8版本的可能是一个常态，那么或许Java9或者Java10它这种短期支持版本不太会长存，那么可能会寻找更长支持的版本。

### Java5 语法特性

| 语法特性             | Spring支持版本 | 代表实现                   |
| -------------------- | -------------- | -------------------------- |
| 注解(Annotation)     | 1.2 +          | @Transactional             |
| 枚举(Enumeration)    | 1.2 +          | Propagation                |
| for-each语法         | 3.0+           | AbstractApplicationContext |
| 自动装箱(AutoBoxing) | 3.0+           |                            |
| 泛型(Generic)        | 3.0+           | ApplicationListener        |

* 注解(Annotation)：

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

### 7.1 JDK 核心 API

| JDK 版本 | 核心 API                                                     |
| -------- | ------------------------------------------------------------ |
| < Java5  | 反射(Reflection)<br/>Java Beans<br/>动态代理(Dynamic Proxy)  |
| Java5    | 并发框架(J.U.C)<br/>格式化(Formatter)<br/>Java管理扩展(JMX)<br/>Instrumentation<br/>XML处理(DOM、SAX、XPath、<br/>XSTL) |
| Java6    | JDBC 4.0 (JSR 221)<br/>JAXB 2.0 (JSR 222)<br/>可插拔注解处理API(JSR269)<br/>Common Annotations (JSR 250)<br/>Java Compiler API (JSR 199)<br/>Scripting in JVM (JSR 223) |
| Java7    | NIO 2 (JSR 203)<br/>Fork/Join框架(JSR 166)<br/>invokedynamic字节码(JSR 292) |
| Java8    | Stream API (JSR 335)<br/>CompletableFuture (J.U.C)<br/>Annotation on Java Types (JSR 308)<br/>Date and Time API (JSR 310)<br/>可重复Annotations(JSR 337)<br/>JavaScript运行时(JSR 223) |
| Java9    | Reactive Streams Flow API (J.U.C)<br/>Process API Updates (JEP 102)<br/>Variable Handles (JEP 193)<br/>Method Handles (JEP 277)<br/>Spin-Wait Hints (JEP 285)<br/>Stack-Walking API (JEP 259) |

**tips: **JSR 是 Java Specification Request 的缩写，是 Java 规范请求

### 7.2 Spring 对 JDK API 实践

### < Java 5 API

| API类型                 | Spring支持版本 | 代表实现                    |
| ----------------------- | -------------- | --------------------------- |
| 反射(Reflection)        | 1.0 +          | MethodMatcher               |
| Java Beans              | 1.0 +          | Cachedlntrospection Results |
| 动态代理(Dynamic Proxy) | 1.0 +          | JdkDynamicAopProxy          |
| XML 处理(DOM,SAX...)    | 1.0 +          | XmlBeanDefinition Reader    |
| Java管理扩展(JMX)       | 1.2 +          | @ManagedResource            |
| Instrumentation         | 2.0 +          | InstrumentationSavingAgent  |
| 并发框架(J.U.C)         | 3.0+           | ThreadPoolTaskScheduler     |
| 格式化(Formatter)       | 3.0+           | DateFormatter               |

**tips:** J.U.C 是 java.util.cocurrent 的缩写

### Java 6 API

| API类型                      | Spring支持版本 | 代表实现                          |
| ---------------------------- | -------------- | --------------------------------- |
| JDBC 4.0 (JSR 221)           | 1.0+           | JdbcTemplate                      |
| Common Annotations (JSR 250) | 2.5+           | CommonAnnotationBeanPostProcessor |
| JAXB 2.0 (JSR 222)           | 3.0+           | Jaxb2Marshaller                   |
| Scripting in JVM (JSR 223)   | 4.2+           | StandardScriptFactory             |
| 可插拔注解处理API(JSR 269)   | 5.0+           | @Indexed                          |
| Java Compiler API (JSR 199)  | 5.0+           | TestCompiler(单元测试)            |

* JAXB 是 Java API for XML Binding 的缩写，就是说 JavaAPI 去绑定 XML 的实现，里面就会有个 marshal、unmarshal 的操作。

* 由于Spring Boot 大量的支持之后，注解的使用需求出现了急剧性的膨胀，这个事情出现一个问题，注解的实现就来自于两个方面，一个是 ASM，一个是属于标准的 Java 反射，那么无论哪种方式都是在运行时的时候来进行实现的，那么有没有一种方法通过编译时来进行实现，那么方法是有的，那么我们说的 @Indexed 就是在我们传统的 Component 的基础上面，编译时把我的API去做一个相当于说建立索引能够帮助我快速的定位到到底哪个类建了 Component 索引，那么这时候我就可以定位到类而不需要逐一的将所有类进行逐一扫描。我们知道比如说我们说 Spring Framework里面有个标准的注解@ComponentScan，那么 scan 里面会指定一个 basePackages，那么你可以指定一个或多个这样的一个标准路径来进行扫描。那么这个注解打完之后，在编译时的时候它不需要扫描只要读取一个归位的路径得到了一个索引文件然后得到相应的类那么相当于说就减少了一个 scanning，一个运行时时间上操作

### Java 7 API

可以使用Markdown表格来整理这些内容，如下所示：

| API类型                | Spring支持版本 | 代表实现                |
| ---------------------- | -------------- | ----------------------- |
| Fork/Join框架(JSR 166) | 3.1+           | ForkJoinPoolFactoryBean |
| NIO 2 (JSR 203)        | 4.0+           | Path Resource           |

### Java 8 API

可以使用Markdown表格来整理这些内容，如下所示：

| API类型                     | Spring支持版本 | 代表实现                             |
| --------------------------- | -------------- | ------------------------------------ |
| Date and Time API (JSR 310) | 4.0+           | DateTimeContext                      |
| 可重复Annotations(JSR 337)  | 4.0+           | @PropertySources                     |
| Stream API (JSR 335)        | 4.2+           | StreamConverter                      |
| CompletableFuture (J.U.C)   | 4.2+           | CompletableToListenableFutureAdapter |

## 8. 对 Java EE API 的整合

### 8.1 Java EE Web 技术相关

| JSR 规范                  | Spring支持版本 | 代表实现                          |
| ------------------------- | -------------- | --------------------------------- |
| Servlet + JSP(JSR 035)    | 1.0+           | DispatcherServlet, JstlView       |
| JSTL(JSR 052)             | 1.0+           | JstlView                          |
| JavaServer Faces(JSR 127) | 2.0-4.2        | FacesContextUtils                 |
| Portlet(JSR 168)          | 2.0-4.2        | DispatcherPortlet                 |
| SOAP(JSR 067)             | 2.5+           | SoapFaultException                |
| WebServices(JSR 109)      | 2.5+           | CommonAnnotationBeanPostProcessor |
| WebSocket(JSR 356)        | 4.0+           | WebSocketHandler                  |

* Servlet + JSP(JSR 035)：是 Spring MVC 的一个核心实现标准，那么 Servlet 有 API，JSP 也有 API,这就会涉及两部分内容，通常来说我们最熟悉的就是 DispatcherServlet，他最核心的还是 Servlet API 的一个运用，包括他有很多 FrameworkServlet 的一个知识的扩展。除此之外 DispatcherSerlet 还负责一个事情，就是视图的渲染，包括 JSP 的视图

* JSTL(JSR 052)：在 JSP 基础上做一些标签的扩展，比如说我们熟悉的 Struts 标签也是个方面

* JavaServer Faces(JSR 127)：视图渲染技术，通过服务端的方式来保存一些状态

* Portlet(JSR 168)：和 Servlet 是对应的，Servlet 主要关注 Web 方面，Portlet 主要关注于我们的门户，那么门户其实之前有个单独的实现，就是 JSR168 实现

* SOAP(JSR 067)：SOAP 协议是 WebService 的一个通讯协议，全称是 Simple Object Access Protocol（简单对象访问协议）

### 8.2 Java EE 数据存储相关

| JSR 规范                   | Spring支持版本 | 代表实现              |
| -------------------------- | -------------- | --------------------- |
| JDO(JSR 12)                | 1.0-4.2        | JdoTemplate           |
| JTA(JSR 907)               | 1.0+           | JtaTransactionManager |
| JPA(EJB 3.0 JSR 220的成员) | 2.0+           | JpaTransactionManager |
| Java Caching API(JSR 107)  | 3.2+           | JCacheCache           |

* JDO(JSR 12)：JDO其实是JPA早期的一个或者说一个半成品，它在Spring框架里面从1.0到4.2予以支持，那么对应的代表作就是 JdoTemplate，它和 JdbcTemplate 是类似的，都是那种模板的方式来进行回调，那么这个东西在5.0之后不支持了，这里我们看出来这里从1.0到4.2，4.2其实就4X最后一个版本

* JTA(JSR 907)：JTA的全称是Java Transaction API，就是 Java 的一个事务API那么它也是个标准，那么这个标准其实早在1.0就已经支持了，其实 Spring 的我们说的事务的抽象其实并不是它自己发明的，而是在前人基础上面来做了一些封装，那么第二个就是我们的JTA这个情况

* JPA(EJB 3.0 JSR 220的成员)：它是作为 EJB3.0 的一个子成员，比如说EJBJSR220的一个子成员，那么它最新的版本已经到了2.1这个版本，那么JPA我们这里最开始支持的是 JPA1.0 版本，1.0 版本其实是注解驱动的方式进行存储，我们的一个 POJO 就是相当于类似于 Hibernate 的方式进行存储,对应的代表作就是 JpaTransactionManager,一个比较有意思的是在 JTA 里面也是叫 Transaction Manager，在JPA里面也有Transaction Manager，那么就说明什么，它们在事务上面Spring做了统一的封装

* Java Caching API(JSR 107)：那么就是JSR 107这个API其实是一个NoSQL的一个实现，就说它就是 Key-Value 存储，那么这个版本也是较晚支持的，也就是说在 Spring3.2 开始支持的，那么这里有一个实现，就是说Spring自己有个 Cache 的一个实现，那么结合它的实现来进行统一的实现

### 8.3 Java EE Bean 技术相关

| JSR 规范                               | Spring支持版本 | 代表实现                             |
| -------------------------------------- | -------------- | ------------------------------------ |
| JMS(JSR 914)                           | 1.1+           | JmsTemplate                          |
| EJB 2.0 (JSR 19)                       | 1.0+           | AbstractStatefulSessionBean          |
| Dependency Injection for Java(JSR 330) | 2.5+           | AutowiredAnnotationBeanPostProcessor |
| Bean Validation(JSR 303)               | 3.0+           | LocalValidatorFactoryBean            |

* JMS(JSR 914)：看起来和我们的 Java Bean 没有太大关系，JMS 其实在 EJB 里面有另外一个类似的称呼，叫做消息驱动 Bean，就是 Message Driven 然后 Bean 的一个方式。

  那么JMS它的JSR规范是914，同时它在1.1的时候就开始予以支持了，那么Spring它也支持到JMS的一个1.2的规范。那么我们可以看出来它也是这种Template的方式来进行操作，就是JmsTemplate。那么和我们Jdbc它们俩也是类似的

* EJB 2.0 (JSR 19)：我们说JMS早期是用到 EJB 里面的，所以 EJB2.0 的时候就已经开始支持了，那么也包括了 EJB 里面主要的有三种，有代表性的一个 Bean，一种是有状态 Bean，就是我们后面看到一个代表实现，这里是AbstractStatefulSession，那么就是有状态 Bean。有一种是无状态 Bean 就是 Stateless，就是说它这种无状态的 Bean。第三种 Bean 就是我们说的消息驱动 Bean，还有第四种，就是说我们把无状态 Bean和有状态 Bean 都分为会话 Bean 就是 SessionBean，那么第三种就是我们的JPA里面的存储的 Bean，就是我们持久化那个 Bean，这是我们前面所提到的事情

* Dependency Injection for Java(JSR 330)：依赖注入 for API，那么依赖注入for API 其实这个东西也要感谢 Spring，这个地方是 Rod Johnson 来 Leader 这个 JSR 来进行支持的，也就是说他把以往的 Spring 里面的 Autowired 注解变成了 Injected 的注解提交给了JSR官方也是提交给Java的官方，然后他们来进行讨论。那么就成功地引用去了，那么这个实现就说在Spring里面的Autowired实现和Injected实现都放在了AutowiredAnnotationBeanPostProcessor代码里来实现
* Bean Validation(JSR 303)：它其实也是由Hibernate团队里面的人来进行维护和进行引导的，那么在Spring 3.0版本开始就已经开始支持了，那么这个版本里面就会有一个非常有意思情况，就是说其实它是一个适配情况，就是说它把Spring校验和Bean的校验的规范做了融合，就形成了 LocalValidatorFactoryBean

**tips：**

JSR官方网址:https://jcp.org/
小马哥JSR收藏:https://github.com/mercyblitz/jsr
Spring官方文档根路径:https://docs.spring.io/spring/docs/

## 9. 编程模型

### 9.1 面向对象编程

面向对象编程

契约接口:Aware、BeanPostProcessor...

设计模式:观察者模式、组合模式、模板模式...

对象继承:Abstract*类

* Aware 接口

  ```java
  package org.springframework.beans.factory;
  
  public interface Aware {
  
  }
  ```

  Aware 接口其实是 Spring3.1 提供的一个新的接口，那么它比较核心的一个接口是什么，就是 ApplicationContextAware

  ```java
  package org.springframework.context;
  
  import org.springframework.beans.BeansException;
  import org.springframework.beans.factory.Aware;
  
  public interface ApplicationContextAware extends Aware {
  
  	void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
  
  }
  ```

  Aware 它就是一个模式，就是说它每一种前面是它的一个类型，那么它要 Aware 什么东西，就会显示 ApplicationContextAware，这种方式会有一个 Set 的方法，那么会把对应的类型传递过来。

  那么同理可得 BeanFactory，那么这里会把BeanFactory给做回来，那么当然还有其他几种，那么这种方式就是一个接口的方式，那么这个也称为 Aware 接口回调。

  那么每当我的 Bean 去实现接口的时候，回调这么一个对象给我，就是传个对象给我们来进行使用

  ```java
  package org.springframework.beans.factory;
  
  import org.springframework.beans.BeansException;
  
  public interface BeanFactoryAware extends Aware {
  
  	void setBeanFactory(BeanFactory beanFactory) throws BeansException;
  
  }
  ```

* BeanPostProcessor 接口

  ```java
  package org.springframework.beans.factory.config;
  
  import org.springframework.beans.BeansException;
  import org.springframework.lang.Nullable;
  
  public interface BeanPostProcessor {
  
  	@Nullable
  	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
  		return bean;
  	}
  
  	@Nullable
  	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
  		return bean;
  	}
  
  }
  ```

  那么这个接口也是我们经常用到的，就是关于 Bean 的一个生命周期的后置处理，那么包括 Beforelnitialization

* 观察者模式

  ApplicationEvent 就是一个观察者模式的扩展，那么它是基于Java的标准事件

  ```java
  package org.springframework.context;
  
  import java.util.EventObject;
  
  public abstract class ApplicationEvent extends EventObject {
  
  	private static final long serialVersionUID = 7099057708183571937L;
  
  	private final long timestamp;
  
  
  	public ApplicationEvent(Object source) {
  		super(source);
  		this.timestamp = System.currentTimeMillis();
  	}
  
  
  	public final long getTimestamp() {
  		return this.timestamp;
  	}
  
  }
  ```

  EventObject

  ```java
  public class EventObject implements java.io.Serializable {
  
      private static final long serialVersionUID = 5516075349620653480L;
  
      protected transient Object  source;
  
      public EventObject(Object source) {
          if (source == null)
              throw new IllegalArgumentException("null source");
  
          this.source = source;
      }
  
      public Object getSource() {
          return source;
      }
  
      public String toString() {
          return getClass().getName() + "[source=" + source + "]";
      }
  }
  ```

  他有一个简单的实现：SimpleApplicationEventMulticaster，multicastEvent 方法就是一个观察者模式，通过事件的方式让我的监听器进行状态的回调或者说一个事件的处理。

* 组合模式

  CompositeCacheManager 多个缓存进行合并

  ```java
  public class CompositeCacheManager implements CacheManager, InitializingBean {
  ```

* 模板模式

​	JdbcTemplate

* 对象继承

  AbstractApplicationContext、AbstractBeanFactory

### 9.2 面向切面编程

动态代理:JdkDynamicAopProxy

字节码提升:ASM、CGLib、AspectJ...

### 9.3 面向元编程

注解:模式注解(@Component、@Service、@Respository...)

配置:Environment抽象、PropertySources、BeanDefinition...

泛型:GenericTypeResolver、ResolvableType...

### 9.4 函数驱动

函数接口:ApplicationEventPublisher
Reactive: Spring WebFlux

### 9.5 模块驱动

Maven Artifacts
OSGI Bundles
Java 9 Automatic Modules
Spring @Enable*

## 10. Spring 核心价值

<img src="https://s3.bmp.ovh/imgs/2023/05/06/2d88c4e345076d6a.png"  />

## 11. 面试题

**<font color="green" size="2">沙雕面试题</font>**-什么是 Spring Framework？

* 官方介绍

  The Spring Framework provides a comprehensive programmingand configuration model for modern Java-based

  enterprise applications - on any kind of deployment platforrm.

  Spring 框架提供现代基于 Java 的企业应用程序的全面编程和配置模型，适用于任何类型的部署平台。

  A key element of Spring is infrastructural support at the appplication level: Spring focuses on the "plumbing" of enterprisse

  applications so that teams can focus on application-level buusiness logic, without unnecessary ties to specific deployment

  environments.

  Spring 的一个关键要素是应用程序级别的基础设施支持：Spring 专注于企业应用程序的“管道”，使团队可以专注于应用程序级别的业务逻辑，而不必与特定的部署环境产生不必要的联系。

* **官方文档描述**

  Spring makes it easy to create Java enterprise applicattions

  It provides everything you need to embrace the Java language iin

  an enterprise environment, with support for Groovy and Koflin as

  alternative languages on the JVM, and with the flexibility to

  create many kinds of architectures depending on an

  application's needs.

  Spring 使创建 Java 企业应用程序变得简单。它提供了您在企业环境中使用 Java 语言所需的一切支持，同时还支持在 JVM 上使用 Groovy 和 Kotlin 等备选语言，并具有根据应用程序需求创建多种架

  构的灵活性。

**<font color="orange" size="2">996面试题</font>**-Spring Framework有哪些核心模块？

* spring-core:Spring 基础 API 模块,如资源管理,泛型处理

* spring-beans:Spring Bean 相关,如依赖查找,依赖注入

* spring-aop:SpringAOP 处理,如动态代理,AOP 字节码提升

* spring-context:事件驱动、注解驱动,模块驱动等

* spring-expression:Spring 表达式语言模块

**<font color="red" size="2">劝退面试题</font>**-Spring Framework 的优势和不足是什么？



__本节完__

