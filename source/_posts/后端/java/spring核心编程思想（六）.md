---
title: Spring 核心编程思想（五）：Spring IoC 依赖注入
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/05/19/b5298159ed069919.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. 内容提要

![](https://s3.bmp.ovh/imgs/2023/06/15/cb467eb7b389a491.png)

## 2. 依赖查找的前世今生

1. 单一类型依赖查找

* JNDI - javax.naming.Context#lookup(javax.naming.Name)

* JavaBeans - java.beans.beancontext.BeanContext

2. 集合类型依赖查找

* java.beans.beancontext.BeanContext

3. 层次性依赖查找

* java.beans.beancontext.BeanContext

It is desirable to both provide a logical, traversable, hierarchy of JavaBeans, and further to provide a general mechanism whereby an object instantiating an arbitrary JavaBean can offer that JavaBean a variety of services, or interpose itself between the uniderlying system service and the JavaBean, in a conventional fashion.

这段文字主要讲述了在 Java 编程中，提供一个有逻辑和可遍历的 JavaBean 层次结构的需求，同时还需要提供一种通用机制，使得实例化 JavaBean 对象的对象可以向其提供各种服务或者在系统服务和JavaBean 之间插入自己。通俗的说，就是需要建立一个 JavaBean 的层次结构，并提供一种机制，以便在 JavaBean 使用过程中可以提供服务。具体来说，要实现这个需求，需要考虑以下几个方面：

1. JavaBean 的层次结构：可以使用组合模式来实现 JavaBean 的逻辑、可遍历的层次结构，以便可以按照树形结构来查找和访问 JavaBean。

2. 服务提供机制：可以使用反射机制和注入等技术来实现服务提供机制，使得可以在 JavaBean 实例化时向其注入服务对象，并在需要时调用服务对象的方法来为 JavaBean 提供服务。

3. 中介模式：可以使用代理或装饰器等模式来实现中介模式，使得 JavaBean 可以通过中介对象来访问系统服务或者其他 JavaBean。通过中介对象，可以在系统服务和 JavaBean 之间添加额外的逻辑处理。

综上所述，建立一个 JavaBean 的层次结构并提供服务机制是 Java 编程中常见的需求，可以通过组合模式、反射机制、注入、代理或装饰器等技术来实现。

![javabeans](https://s3.bmp.ovh/imgs/2023/05/19/7f7b7f4337b8091c.png)

## 3. 单一依赖查找

单一类型依赖查找接口-BeanFactory

1. 根据 Bean 名称查找

* getBean(String)

* Spring 2.5 覆盖默认参数:getBean(String,Object...)

2. 根据 Bean 类型查找

* Bean 实时查找

  * Spring 3.0 getBean(Class)

  * Spring 4.1 覆盖默认参数:getBean(Class,Object...)

**tips:**关于覆盖参数这个方法的调用，建议大家不需要去掌握，也不需要去运用，因为这个非常危险。因为这个接口会覆盖掉默认的一些参数，同时我们会看到 API 里面有这么一个描述

```java
/**
 * Return an instance, which may be shared or independent, of the specified bean.
 * <p>Allows for specifying explicit constructor arguments / factory method arguments,
 * overriding the specified default arguments (if any) in the bean definition.
 * @param name the name of the bean to retrieve
 * @param args arguments to use when creating a bean instance using explicit arguments
 * (only applied when creating a new instance as opposed to retrieving an existing one)
 * @return an instance of the bean
 * @throws NoSuchBeanDefinitionException if there is no such bean definition
 * @throws BeanDefinitionStoreException if arguments have been given but
 * the affected bean isn't a prototype
 * @throws BeansException if the bean could not be created
 * @since 2.5
 */
Object getBean(String name, Object... args) throws BeansException;
```

实例可能是 shared 也可能是 independent，这个 shared 就是指的单例，那么 independent 主要是原生。这里就会告诉你一个不好的特点，如果当你是 shared 的话，你每调用一次就会覆盖它的方法，这种方式实际上是有点不可取的。

* Spring 5.1 Bean 延迟查找
  * getBeanProvider(Class)
  * getBeanProvider(ResolvableType)

BeanFactory:

```java
<T> ObjectProvider<T> getBeanProvider(Class<T> requiredType);
```

ObjectProvider:

```java
public interface ObjectProvider<T> extends ObjectFactory<T>, Iterable<T> {...
```

OjbectProvider 是继承了 ObjectFactory，ObjectFactory 它就通过某种参数关联类型去关联一个特性

```java
/**
 * <h1>通过{@link org.springframework.beans.factory.ObjectProvider}进行依赖查找</h1>
 *
 * @author 风间
 * @since 2023/5/19
 */
public class ObjectProviderDemo {

    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(ObjectProviderDemo.class);
        applicationContext.refresh();

        lookupByObjectProvider(applicationContext);

        applicationContext.close();
    }

    @Bean
    public String hello() {// 方法名就是 Bean 名称 = "hello"
        return "hello";
    }

    private static void lookupByObjectProvider(AnnotationConfigApplicationContext applicationContext) {

        ObjectProvider<String> beanProvider = applicationContext.getBeanProvider(String.class);
        String object = beanProvider.getObject();
        System.out.println(object);

    }
}
```

3. 根据 Bean 名称 + 类型查找:getBean(String,Class)

## 4. 集合类型依赖查找

集合类型依赖查找接口-ListableBeanFactory

1. 根据 Bean 类型查找

* 获取同类型 Bean 名称列表
  * getBeanNamesForType(Class)
  * Spring 4.2 getBeanNamesForType(ResolvableType)

* 获取同类型 Bean 实例列表
  * getBeansOfType(Class)以及重载方法

2. 通过注解类型查找

* Spring 3.0 获取标注类型 Bean 名称列表
  * getBeanNamesForAnnotation(Class<? extends Annotation>)

* Spring 3.0 获取标注类型 Bean 实例列表
  * getBeansWithAnnotation(Class<? extends Annotation>)

* Spring 3.0 获取指定名称+标注类型 Bean 实例
  * findAnnotationOnBean(String,Ciass<? extends Annotation>)

## 5. 层次性依赖查找

层次性依赖查找接口-HierarchicalBeanFactory

1. 双亲 BeanFactory:getParentBeanFactory()

2. 层次性查找

* 根据 Bean 名称查找
  * 基于 containsLocalBean 方法实现

* 根据 Bean 类型查找实例列表
  * 单一类型:BeanFactoryUtils#beanOfType
  * 集合类型:BeanFactoryUtils#beansOfTypelncludingAncestors

* 根据 Java 注解查找名称列表
  * BeanFactoryUtils#beanNamesForTypelncludingAncestors

```java
public static void main(String[] args) {

    // 创建容器
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
    // 将当前类 AnnotationApplicationContextAsIoCContainerDemo 作为配置类（Configuration Class）
    applicationContext.register(AnnotationApplicationContextAsIoCContainerDemo.class);
    // 启动应用上下文
    applicationContext.refresh();

    // 1. 获取 HierarchicalBeanFactory <- ConfigurableBeanFactory <- ConfigurableListableBeanFactory
    ConfigurableListableBeanFactory beanFactory = applicationContext.getBeanFactory();
    System.out.println("获取 BeanFactory 的 Parent BeanFactory : " + beanFactory.getParentBeanFactory());
    // 2. 设置 Parent BeanFactory
    HierarchicalBeanFactory parentBeanFactory = crateParentBeanFactory();
    beanFactory.setParentBeanFactory(parentBeanFactory);
    System.out.println("获取 BeanFactory 的 Parent BeanFactory : " + beanFactory.getParentBeanFactory());
	
    displayLocalBean(beanFactory,"user");
    displayLocalBean(parentBeanFactory,"user");
	
    displayContainsBean(beanFactory,"user");
    displayContainsBean(parentBeanFactory,"user");
    // 关闭应用上下文
    applicationContext.close();
}

public static void displayContainsBean(HierarchicalBeanFactory beanFactory,String beanName){

    System.out.printf("当前 BeanFactory[%s] 是否包含 bean[name : %s]: %s\n", beanFactory, beanName,
            containsBean(beanFactory,beanName));
}

private static boolean containsBean(HierarchicalBeanFactory beanFactory,String beanName){
    BeanFactory parentBeanFactory = beanFactory.getParentBeanFactory();
    if (parentBeanFactory instanceof HierarchicalBeanFactory) {
        HierarchicalBeanFactory parentHierarchicalBeanFactory = HierarchicalBeanFactory.class.cast(parentBeanFactory);
        if (containsBean(parentHierarchicalBeanFactory,beanName)) {
            return true;
        }
    }
    return beanFactory.containsLocalBean(beanName);
}

private static void displayLocalBean(HierarchicalBeanFactory beanFactory, String beanName) {

    System.out.printf("当前 BeanFactory[%s] 是否包含 bean[name : %s]: %s\n", beanFactory, beanName,
            beanFactory.containsLocalBean(beanName));
}

private static ConfigurableListableBeanFactory crateParentBeanFactory() {
    // 创建 BeanFactory 容器
    DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
    XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);
    // XML 配置文件 classpath 路径
    String location = "META-INF/dependency-injection-context.xml";
    // 加载配置，返回加载到的 Bean 个数
    reader.loadBeanDefinitions(location);
    return beanFactory;

}
```

## 6. 延迟依赖查找

Bean延迟依赖查找接口

* org.springframework.beans.factory.ObjectFactory

* org.springframework.beans.factory.ObjectProvider
  * Spring5 对 Java8 特性扩展
    * 函数式接口
      * getIfAvailable(Supplier)
      * ifAvailable(Consumer)
    * Stream 扩展-stream()

getIfAvailable:类型安全，当 Bean 不存在的时候会如何处理

```java
private static void lookupIfAvailable(AnnotationConfigApplicationContext applicationContext) {
    ObjectProvider<User> beanProvider = applicationContext.getBeanProvider(User.class);
    User user = beanProvider.getIfAvailable(User::new);
}
```

stream:

```java
private static void lookupByStreamOps(AnnotationConfigApplicationContext applicationContext) {
    ObjectProvider<String> beanProvider = applicationContext.getBeanProvider(String.class);
    Iterable<String> stringIterable = beanProvider;
    for(String s:stringIterable){
        System.out.println(s);
    }
}

private static void lookupByStreamOps(AnnotationConfigApplicationContext applicationContext) {
    ObjectProvider<String> beanProvider = applicationContext.getBeanProvider(String.class);
//        Iterable<String> stringIterable = beanProvider;
//        for(String s:stringIterable){
//            System.out.println(s);
//        }
    beanProvider.stream().forEach(System.out::println);
}
```

## 7. 安全依赖查找

| 依赖查找类型                       | 代表实现                             | 是否安全 |
| ---------------------------------- | ------------------------------------ | -------- |
| 单一类型查找                       |                                      |          |
| BeanFactory#getBean                | 代表实例对象的工厂类                 | 否       |
| ObjectFactory#getObject            | 简单对象实例化的工厂类               | 否       |
| ObjectProvider#getIfAvailable      | 延迟查找和依赖注入的工厂类           | 是       |
| 集合类型查找                       |                                      |          |
| ListableBeanFactory#getBeansOfType | 可罗列指定类型所有实例对象的工厂类   | 是       |
| ObjectProvider#stream              | 延迟查找和依赖注入的工厂类的流式接口 | 是       |

其中，BeanFactory#getBean、ObjectFactory#getObject 和 ObjectProvider#getIfAvailable 均属于单一类型查找方法，分别代表实例对象的工厂类、简单对象实例化的工厂类以及延迟查找和依赖注入的工厂类。而在集合类型查找中，ListableBeanFactory#getBeansOfType 可罗列指定类型所有实例对象，而 ObjectProvider#stream 则为延迟查找和依赖注入的工厂类的流式接口。

**tips:**层次性依赖查找的安全性取决于其扩展的单一或集合类型的 BeanFactory 接口

## 8. 内建可查找的依赖

哪些 Spring IoC 内建依赖可供查找？

* AbstractApplicationContext 内建可查找的依赖

| Bean名称                    | Bean实例                         | 使用场景                                      |
| --------------------------- | -------------------------------- | --------------------------------------------- |
| environment                 | Environment 对象                 | 外部化配置以及 Profiles                       |
| systemProperties            | java.util.Properties 对象        | Java系统属性                                  |
| systemEnvironment           | java.util.Map 对象               | 操作系统环境变量                              |
| messageSource               | MessageSource 对象               | 国际化文案                                    |
| lifecycleProcessor          | LifecycleProcessor 对象          | Lifecycle Bean 处理器                         |
| applicationEventMulticaster | ApplicationEventMulticaster 对象 | Spring 事件广播器，用于发送和接收 Spring 事件 |

以上是常用的在 Spring 框架中使用的 Bean 名称、Bean 实例以及它们的使用场景。这些 Bean 都是 Spring 框架非常重要的组成部分，用于在应用程序中支持各种不同类型的功能。

Environment 对象可以用来管理应用程序的配置信息，并根据不同的 Profiles 进行适当的配置。systemProperties 和 systemEnvironment 都是用来访问 Java 虚拟机的系统变量和操作系统的环境变

量的。messageSource 则是用来支持应用程序的国际化功能。lifecycleProcessor 和 applicationEventMulticaster是用来支持Spring框架的生命周期和事件功能的。lifecycleProcessor 会处理

所有实现了 Lifecycle 接口的 Bean，而 applicationEventMulticaster 则会处理所有标注了 @EventListener 注解的方法。

以上这些 Bean 的使用场景非常广泛，在许多企业级应用程序中都得到了广泛的应用。如果你想深入研究 Spring 框架，了解这些 Bean 的原理和使用方法将会对你非常有帮助。

* 注解驱动Spring应用上下文内建可查找的依赖(部分)

| Bean名称                                                     | Bean实例                                  | 使用场景                                     |
| ------------------------------------------------------------ | ----------------------------------------- | -------------------------------------------- |
| org.springframework.context.annotation.internalConfigurationAnnotationProcessor | ConfigurationClassPostProcessor 对象      | 处理 Spring 配置类                           |
| org.springframework.context.annotation.internalAutowiredAnnotationProcessor | AutowiredAnnotationBeanPostProcessor 对象 | 处理 @Autowired 以及 @Value注解              |
| org.springframework.context.annotation.internalCommonAnnotationProcessor | CommonAnnotationBeanPostProcessor 对象    | 处理 JSR-250 注解,如 @PostConstruct等        |
| org.springframework.context.event.internalEventListenerProcessor | EventListenerMethodProcessor 对象         | 处理标注 @EventListener 的Spring事件监听方法 |

以上是四个常见的Spring框架中的Bean名称、Bean实例以及它们的使用场景。这些Bean都是Spring框架中非常重要的组成部分，用于在应用程序中处理配置信息、依赖注入、事件触发等关键功能。

ConfigurationClassPostProcessor对象用于解析@Configuration注解和@Bean注解，将它们转换成Spring容器中的Bean定义。AutowiredAnnotationBeanPostProcessor对象则可以自动装配Bean之

间的依赖关系。CommonAnnotationBeanPostProcessor对象主要处理JSR-250注解，如@PostConstruct和@PreDestroy等。最后，EventListenerMethodProcessor对象用于处理标注了

@EventListener 注解的 Spring 事件监听方法。

这些 Spring Bean 的使用场景都非常广泛，在许多企业级应用程序中都得到了广泛的应用。如果你想深入研究 Spring 框架，了解这些 Bean 的原理和使用方法将会对你非常有帮助。

* 注解驱动Spring应用上下文内建可查找的依赖(续)

| Bean名称                                                     | Bean实例                                   | 使用场景                                                     |
| ------------------------------------------------------------ | ------------------------------------------ | ------------------------------------------------------------ |
| org.springframework.context.event.internalEventListenerFactory | DefaultEventListenerFactory对象            | 将带有@EventListener注解的方法适配为ApplicationListener      |
| org.springframework.context.annotation.internalPersistenceAnnotationProcessor | PersistenceAnnotationBeanPostProcessor对象 | 处理带有JPA注解的类和方法，例如@Entity、@MappedSuperclass、@PersistenceContext等 |

以上是在Spring框架中常用的两个Bean名称、Bean实例以及它们的使用场景。这些Bean都是Spring框架中非常重要的组成部分之一。

DefaultEventListenerFactory对象主要用于将标注了@EventListener注解的方法转换为合适的ApplicationListener对象。这样，当事件发生时，对应的ApplicationListener就能够接收到相应的事

件，并作出相应的处理。另外，这个Bean也可以用来对事件监听器进行管理，例如添加或删除监听器等。

另一个常用的Bean是PersistenceAnnotationBeanPostProcessor对象，它会处理带有JPA注解的类和方法，并自动将相关的Bean注册到Spring容器中。这个Bean主要用于简化使用JPA进行数据库操作的流

程，使操作更加方便和灵活。

需要注意的是，org.springframework.context.annotation.internalPersistenceAnnotationProcessor这个Bean的使用是有条件的，需要通过在类路径下存在JPA相关的Library包才能够启动。如

果不满足这个条件，这个Bean则不会被初始化。

以上这些Bean在Spring框架中都扮演着非常重要的角色，它们能够帮助开发者更加高效地进行开发工作，同时也能够提高程序的性能和健壮性。

## 9.依赖查找中的经典异常

* BeansException 子类型

| 异常类型                        | 触发条件                                        | 场景举例                                                     |
| ------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| NoSuchBeanDefinitionException   | 当查找 Bean 不存在于IoC 容器时                  | 调用 BeanFactory 的 getBean()、ObjectFactory 的 getObject() 或 BeanFactory 的 getBean(Class)方法，传入一个未在IoC容器中注册的 Bean 名称或类型，将会抛出此异常。 |
| NoUniqueBeanDefinitionException | 类型依赖查找时，IoC 容器存在多个Bean 实例       | 调用 BeanFactory 的 getBean() 或 ApplicationContext 的 getBean() 方法，传入一个接口或抽象类类型，如果 IoC 容器中存在多个实现类，则会抛出此异常。 |
| BeanInstantiationException      | 当 Bean 所对应的类型非具体类时                  | 调用 BeanFactory 或 ApplicationContext 的getBean() 方法，传入一个接口或抽象类类型，或者是一个无法实例化的具体类类型时，将会抛出此异常。 |
| BeanCreationException           | 当 Bean 初始化过程中，Bean 初始化方法执行异常时 | 在 Bean 的初始化方法中抛出异常时，将会导致 BeanCreationException 异常的抛出。 |
| BeanDefinitionStoreException    | 当 BeanDefinition 配置元信息非法时              | Bean 定义文件中存在语法错误、引用了不存在的类或属性、或者是 XML 配置资源无法打开等情况时，将会抛出此异常。 |

以上是 Spring 框架中常见的 BeansException 子类型异常，每种异常都有自己特定的触发条件和场景举例。当我们在开发过程中遇到这些异常时，需要根据异常信息对代码进行调试和修复，以保证程序的正常运行。同时，也可以从这些异常中获取到一定的启示，提高代码的质量和健壮性。

## 12. 面试题

**<font color="green" size="2">沙雕面试题</font>**-什么是 Spring IoC 容器？



**<font color="orange" size="2">996面试题</font>**-BeanFactory 和 FactoryBean 的区别？



**<font color="red" size="2">劝退面试题</font>**-Spring IoC 容器启动时做了哪些准备?？





__本节完__
