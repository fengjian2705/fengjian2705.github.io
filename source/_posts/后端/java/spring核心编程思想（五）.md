---
title: Spring 核心编程思想（五）：Spring 依赖查找
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2023/05/19/b5298159ed069919.jpg
# excerpt: spring
categories:
  - 后端
  - spring 核心编程思想
---

## 1. Spring IoC 依赖查找

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

## 2. 依赖查找的前世今生

1. 单一类型依赖查找

* JNDI - javax.naming.Context#lookup(javax.naming.Name)

* JavaBeans - java.beans.beancontext.BeanContext

2. 集合类型依赖查找

* java.beans.beancontext.BeanContext

3. 层次性依赖查找

* java.beans.beancontext.BeanContext

It is desirable to both provide a logical, traversable, hierarchy of JavaBeans, and further to provide a general mechanism whereby an object instantiating an arbitrary JavaBean can offer that JavaBean a variety of services, or interpose itself between the uniderlying system service and the JavaBean, in a conventional fashion.

这段文字主要讲述了在 Java 编程中，提供一个有逻辑和可遍历的 JavaBean 层次结构的需求，同时还需要提供一种通用机制，使得实例化 JavaBean 对象的对象可以向其提供各种服务或者在系统服务和JavaBean 之间插入自己。通俗的说，就是需要建立一个 JavaBean 的层次结构，并提供一种机制，以便在 JavaBean 使用过程中可以提供服务。

具体来说，要实现这个需求，需要考虑以下几个方面：

1. JavaBean 的层次结构：可以使用组合模式来实现 JavaBean 的逻辑、可遍历的层次结构，以便可以按照树形结构来查找和访问 JavaBean。

2. 服务提供机制：可以使用反射机制和注入等技术来实现服务提供机制，使得可以在JavaBean实例化时向其注入服务对象，并在需要时调用服务对象的方法来为 JavaBean 提供服务。

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

实例可能是 shared 可能是 independent，这个 shared 就是指的单例，那么 independent 主要是原生。这里就会告诉你一个不好的特点，如果当你是 shared 的话，你每调一次就会覆盖它的方法，这种方式实际上是有点不可取的。

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



## 9.依赖查找中的经典异常



## 12. 面试题

**<font color="green" size="2">沙雕面试题</font>**-什么是 Spring IoC 容器？



**<font color="orange" size="2">996面试题</font>**-BeanFactory 和 FactoryBean 的区别？



**<font color="red" size="2">劝退面试题</font>**-Spring IoC 容器启动时做了哪些准备?？





__本节完__
