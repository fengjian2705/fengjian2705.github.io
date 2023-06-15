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

## 1. 内容提要

![](https://s3.bmp.ovh/imgs/2023/06/15/57545356a25571a3.png)

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

  每个 Bean 拥有一个或多个标识符(identifiers),这些标识符在 Bean 所在的容器必须是唯一的。通常一个 Bean 仅有一个标识符,如果需要额外的,可考虑使用别名(Alias)来扩充。

  在基于 XML 的配置元信息中,开发人员可用 id 或者 name 属性来规定 Bean 的标识符。通常 Bean 的标识符由字母组成,允许出现特殊字符。如果要想引入 Bean 的别名的话,可在 name 属性使用半角
  
  逗号(",")或分号(";")来间隔。Bean 的 id 或 name 属性并非必须指定,如果留空的话,容器会为 Bean 自动生成一个唯一的名称。Bean 的命名尽管没有限制,不过官方建议采用驼峰的方式,更符合 Java 的命名约定。

​	**tips：**每个 Bean 它的识别符是在它所在的容器，也就说它所在的 BeanDefinition 里面或者说 BeanFactory 里面是唯一的并非是整个应用是唯一的这个地方是要加以区别的。

* Bean 名称生成器(BeanNameGenerator)

* 由 Spring Framework 2.0.3 引入,框架內建两种实现:

  DefaultBeanNameGenerator：默认通用 BeanNameGenerator 实现

* AnnotationBeanNameGenerator: 基于注解扫描的 BeanNameGenerator 实现,起始于 Spring Framework 2.5,关联的官方文档:

  With component scanning in the classpath, Spring generates bean names for unnamed components,following the rules described earlier: essentially,taking 

  the simple class name and turning its initial character to lower-case. However, in the (unusual) special case when there is more than one character and 

  both the first and second characters are upper case, the original casing gets preserved. These are the same rules as defined by 

  java.beans.Introspector.decapitalize (which Spring uses here).

  当在 classpath 中使用组件扫描时，Spring 会根据之前描述的规则为未命名的组件生成 bean 名称，即将**类名开头字母转换为小写**。但是，在一个罕见的特殊情况下，如果**类名超过一个字符且前两个字        **
  
  **符都是大写字母，则保留原始大小写**。这些规则与 java.beans.Introspector.decapitalize 所定义的规则相同（Spring 在此处使用它）。

### BeanNameGenerator

```java

package org.springframework.beans.factory.support;

import org.springframework.beans.factory.config.BeanDefinition;

/**
 * Strategy interface for generating bean names for bean definitions.
 *
 * @author Juergen Hoeller
 * @since 2.0.3
 */
public interface BeanNameGenerator {

	/**
	 * Generate a bean name for the given bean definition.
	 * @param definition the bean definition to generate a name for
	 * @param registry the bean definition registry that the given definition
	 * is supposed to be registered with
	 * @return the generated bean name
	 */
	String generateBeanName(BeanDefinition definition, BeanDefinitionRegistry registry);

}
```

DefaultBeanNameGenerator:

```java

package org.springframework.beans.factory.support;

import org.springframework.beans.factory.config.BeanDefinition;

/**
 * Default implementation of the {@link BeanNameGenerator} interface, delegating to
 * {@link BeanDefinitionReaderUtils#generateBeanName(BeanDefinition, BeanDefinitionRegistry)}.
 *
 * @author Juergen Hoeller
 * @since 2.0.3
 */
public class DefaultBeanNameGenerator implements BeanNameGenerator {

	/**
	 * A convenient constant for a default {@code DefaultBeanNameGenerator} instance,
	 * as used for {@link AbstractBeanDefinitionReader} setup.
	 * @since 5.2
	 */
	public static final DefaultBeanNameGenerator INSTANCE = new DefaultBeanNameGenerator();


	@Override
	public String generateBeanName(BeanDefinition definition, BeanDefinitionRegistry registry) {
		return BeanDefinitionReaderUtils.generateBeanName(definition, registry);
	}

}
```

从 5.2 版本开始它用的单例的方式来进行做,那么相当于说它为了节约内存的开销，那么这时候用单例来进行表达就可以了。这里为什么没有把构造器变成 private？我们通常说一个单例我们不希望外部来进行初始化，那么为什么这里不把它变成私有的，因为你这个 BeanDefinition 就是 DefaultBeanNameGenerator 实现它是个公有的 API，如果你擅自从高版本里面把它构造函数的一个我们说访问限定改成了非public 的话那么其他版本其他老的兼容方式就会出问题所以这个时候还是保持这样的原样，那么换言之 Spring 官方希望如果你要用API实现的话最好是用单例的方式来进行呈现。

进一步查看 generateBeanName 方法：

```java
public static String generateBeanName(BeanDefinition beanDefinition, BeanDefinitionRegistry registry)
        throws BeanDefinitionStoreException {

    return generateBeanName(beanDefinition, registry, false);
}
```

```java
public static String generateBeanName(
        BeanDefinition definition, BeanDefinitionRegistry registry, boolean isInnerBean)
        throws BeanDefinitionStoreException {

    String generatedBeanName = definition.getBeanClassName();
    if (generatedBeanName == null) {
        if (definition.getParentName() != null) {
            generatedBeanName = definition.getParentName() + "$child";
        }
        else if (definition.getFactoryBeanName() != null) {
            generatedBeanName = definition.getFactoryBeanName() + "$created";
        }
    }
    if (!StringUtils.hasText(generatedBeanName)) {
        throw new BeanDefinitionStoreException("Unnamed bean definition specifies neither " +
                "'class' nor 'parent' nor 'factory-bean' - can't generate bean name");
    }

    String id = generatedBeanName;
    if (isInnerBean) {
        // Inner bean: generate identity hashcode suffix.
        id = generatedBeanName + GENERATED_BEAN_NAME_SEPARATOR + ObjectUtils.getIdentityHexString(definition);
    }
    else {
        // Top-level bean: use plain class name with unique suffix if necessary.
        return uniqueBeanName(generatedBeanName, registry);
    }
    return id;
}
```

首先我们去生成这个Bean的名称的时候它首先去取的是 Bean 的一个 Class,Bean 的 Class 之后我们可以看一下这里会有两种实现方式:

* 第一个它如果是一个嵌套的 Bean 这个时候会生成一个,就说 Bean 名称后面会增加一个分隔符就是个#号,通过调试就会发现会发现在这个 Name 名后面通常会加上一个井号后面带个数字或带一个字符来进行唯一的标识，

* 如果它是唯一的话，,它会变成一个等于 uniqueBean，这种方式其实比较简单

### AnnotationBeanNameGenerator

```java

package org.springframework.context.annotation;

import java.beans.Introspector;
import java.util.Map;
import java.util.Set;

import org.springframework.beans.factory.annotation.AnnotatedBeanDefinition;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.core.annotation.AnnotationAttributes;
import org.springframework.core.type.AnnotationMetadata;
import org.springframework.lang.Nullable;
import org.springframework.util.Assert;
import org.springframework.util.ClassUtils;
import org.springframework.util.StringUtils;

/**
 * {@link org.springframework.beans.factory.support.BeanNameGenerator}
 * implementation for bean classes annotated with the
 * {@link org.springframework.stereotype.Component @Component} annotation
 * or with another annotation that is itself annotated with
 * {@link org.springframework.stereotype.Component @Component} as a
 * meta-annotation. For example, Spring's stereotype annotations (such as
 * {@link org.springframework.stereotype.Repository @Repository}) are
 * themselves annotated with
 * {@link org.springframework.stereotype.Component @Component}.
 *
 * <p>Also supports Java EE 6's {@link javax.annotation.ManagedBean} and
 * JSR-330's {@link javax.inject.Named} annotations, if available. Note that
 * Spring component annotations always override such standard annotations.
 *
 * <p>If the annotation's value doesn't indicate a bean name, an appropriate
 * name will be built based on the short name of the class (with the first
 * letter lower-cased). For example:
 *
 * <pre class="code">com.xyz.FooServiceImpl -&gt; fooServiceImpl</pre>
 *
 * @author Juergen Hoeller
 * @author Mark Fisher
 * @since 2.5
 * @see org.springframework.stereotype.Component#value()
 * @see org.springframework.stereotype.Repository#value()
 * @see org.springframework.stereotype.Service#value()
 * @see org.springframework.stereotype.Controller#value()
 * @see javax.inject.Named#value()
 */
public class AnnotationBeanNameGenerator implements BeanNameGenerator {

	/**
	 * A convenient constant for a default {@code AnnotationBeanNameGenerator} instance,
	 * as used for component scanning purposes.
	 * @since 5.2
	 */
	public static final AnnotationBeanNameGenerator INSTANCE = new AnnotationBeanNameGenerator();

	private static final String COMPONENT_ANNOTATION_CLASSNAME = "org.springframework.stereotype.Component";


	@Override
	public String generateBeanName(BeanDefinition definition, BeanDefinitionRegistry registry) {
		if (definition instanceof AnnotatedBeanDefinition) {
			String beanName = determineBeanNameFromAnnotation((AnnotatedBeanDefinition) definition);
			if (StringUtils.hasText(beanName)) {
				// Explicit bean name found.
				return beanName;
			}
		}
		// Fallback: generate a unique default bean name.
		return buildDefaultBeanName(definition, registry);
	}

	/**
	 * Derive a bean name from one of the annotations on the class.
	 * @param annotatedDef the annotation-aware bean definition
	 * @return the bean name, or {@code null} if none is found
	 */
	@Nullable
	protected String determineBeanNameFromAnnotation(AnnotatedBeanDefinition annotatedDef) {
		AnnotationMetadata amd = annotatedDef.getMetadata();
		Set<String> types = amd.getAnnotationTypes();
		String beanName = null;
		for (String type : types) {
			AnnotationAttributes attributes = AnnotationConfigUtils.attributesFor(amd, type);
			if (attributes != null && isStereotypeWithNameValue(type, amd.getMetaAnnotationTypes(type), attributes)) {
				Object value = attributes.get("value");
				if (value instanceof String) {
					String strVal = (String) value;
					if (StringUtils.hasLength(strVal)) {
						if (beanName != null && !strVal.equals(beanName)) {
							throw new IllegalStateException("Stereotype annotations suggest inconsistent " +
									"component names: '" + beanName + "' versus '" + strVal + "'");
						}
						beanName = strVal;
					}
				}
			}
		}
		return beanName;
	}

	/**
	 * Check whether the given annotation is a stereotype that is allowed
	 * to suggest a component name through its annotation {@code value()}.
	 * @param annotationType the name of the annotation class to check
	 * @param metaAnnotationTypes the names of meta-annotations on the given annotation
	 * @param attributes the map of attributes for the given annotation
	 * @return whether the annotation qualifies as a stereotype with component name
	 */
	protected boolean isStereotypeWithNameValue(String annotationType,
			Set<String> metaAnnotationTypes, @Nullable Map<String, Object> attributes) {

		boolean isStereotype = annotationType.equals(COMPONENT_ANNOTATION_CLASSNAME) ||
				metaAnnotationTypes.contains(COMPONENT_ANNOTATION_CLASSNAME) ||
				annotationType.equals("javax.annotation.ManagedBean") ||
				annotationType.equals("javax.inject.Named");

		return (isStereotype && attributes != null && attributes.containsKey("value"));
	}

	/**
	 * Derive a default bean name from the given bean definition.
	 * <p>The default implementation delegates to {@link #buildDefaultBeanName(BeanDefinition)}.
	 * @param definition the bean definition to build a bean name for
	 * @param registry the registry that the given bean definition is being registered with
	 * @return the default bean name (never {@code null})
	 */
	protected String buildDefaultBeanName(BeanDefinition definition, BeanDefinitionRegistry registry) {
		return buildDefaultBeanName(definition);
	}

	/**
	 * Derive a default bean name from the given bean definition.
	 * <p>The default implementation simply builds a decapitalized version
	 * of the short class name: e.g. "mypackage.MyJdbcDao" -> "myJdbcDao".
	 * <p>Note that inner classes will thus have names of the form
	 * "outerClassName.InnerClassName", which because of the period in the
	 * name may be an issue if you are autowiring by name.
	 * @param definition the bean definition to build a bean name for
	 * @return the default bean name (never {@code null})
	 */
	protected String buildDefaultBeanName(BeanDefinition definition) {
		String beanClassName = definition.getBeanClassName();
		Assert.state(beanClassName != null, "No bean class name set");
		String shortClassName = ClassUtils.getShortName(beanClassName);
		return Introspector.decapitalize(shortClassName);
	}

}

```

这里使用 @Component 注解及其派生的注解 @Repository @Service @Controller，可以看到 @Repository 注解上面打了一个 @Component 注解

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Repository {

	/**
	 * The value may indicate a suggestion for a logical component name,
	 * to be turned into a Spring bean in case of an autodetected component.
	 * @return the suggested component name, if any (or empty String otherwise)
	 */
	@AliasFor(annotation = Component.class)
	String value() default "";

}

```

我们再来看它的生成方式：

```java
@Override
public String generateBeanName(BeanDefinition definition, BeanDefinitionRegistry registry) {
    if (definition instanceof AnnotatedBeanDefinition) {
        String beanName = determineBeanNameFromAnnotation((AnnotatedBeanDefinition) definition);
        if (StringUtils.hasText(beanName)) {
            // Explicit bean name found.
            return beanName;
        }
    }
    // Fallback: generate a unique default bean name.
    return buildDefaultBeanName(definition, registry);
}
```

第一种方法就是说它如果是一个标注这个 Definition 就说如果你是一个注解的方式会被命名成 AnnotatedBeanDefinition，如果它不是的话它会采用什么采用 Fallback 一个补偿的方式，这种方式呢就和传统的方式没有太大的区别。 

## 5. Spring Bean 的别名

Bean别名(Alias)的价值

* 复用现有的 BeanDefinition

* 更具有场景化的命名方法,比如:

  <alias name="myApp-dataSource" alias="subsystemA-dataSource"/>

  <alias name="myApp-dataSource" alias="subsystemB-dataSource"/>

* XML 配置文件中设置 Bean 别名

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

	<!--复用-->
    <import resource="classpath:/META-INF/dependency-injection-context.xml"/>

    <!--将 Spring 容器中的 Bean 建立/关联别名-->
    <alias name="user" alias="tom-user"/>

</beans>
```

测试

```java
package thinking.in.spring.spring.bean.definition;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import tech.fengjian.ioc.container.overview.domain.User;

/**
 * <h1>Bean 别名示例</h1>
 *
 * @author 风间
 * @since 2023/5/11
 */
public class BeanAliasDemo {

    public static void main(String[] args) {

        // 配置 XML 文件
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:/META-INF/bean-definitions-context.xml");

        User tomUser = (User) beanFactory.getBean("tom-user");
        System.out.println("tomUser: " + tomUser);

        User user = (User) beanFactory.getBean("user");
        System.out.println("user: " + user);

        System.out.println("tomUser == user: " + (tomUser == user));
    }
}
```

## 6. 注册 Spring Bean

BeanDefinition 注册

* XML 配置元信息
  * <bean name="..." ... />

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="tech.fengjian.ioc.container.overview.domain.User">
        <property name="id" value="1"/>
        <property name="name" value="jack"/>
        <property name="age" value="18"/>
    </bean>
</beans>
```

```java
public class DependencyLookupDemo {

    public static void main(String[] args) {

        // 配置 XML 信息
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:/META-INF/dependency-lookup-context.xml");
        User user = (User) beanFactory.getBean("user");
        System.out.println("XML 方式注册 Spring Bean，User：" + user);
    }
}

```

* Java 注解配置元信息

  * @Bean

  * @Component

  * @Import

@Bean 方式：

这里的 new AnnotationConfigApplicationContext(Config.class); 实现了将 Config 类配置为 Spring 的 Bean 对象，并且会将 Config 类中标记为 @Bean 注解的类也加载成 Bean 对象。

new AnnotationConfigApplicationContext(Config.class);传参的方式相比无参构造的话省去了 refresh() 方法。他其实内部执行两步：

register(componentClasses);// 将 Config 类及类中的 @Bean 修饰的注册为 Bean 对象

refresh();// 启动 Spring 应用上下文

```java
ublic class DependencyLookupDemo {

    public static void main(String[] args) {

        // 配置 XML 信息
        // 启动 Spring 应用上下文
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(Config.class);
        Map<String, User> users = applicationContext.getBeansOfType(User.class);
        System.out.println("users:"+users);
        System.out.println("Configs:"+applicationContext.getBeansOfType(Config.class));

    }

    public static class Config{

        @Bean
        public User user(){
            User user = new User();
            user.setId(11L);
            user.setName("林黛玉");
            user.setAge(15);
            return user;
        }

    }
}
```

@Component 方式

`applicationContext.register(Config.class);`并不会加载其他的标记为`@Component`的 Bean，因为它只会注册指定类中声明的 Bean。

如果要让`applicationContext.register()`方法注册其他`@Component`注解的 bean，需要在配置类中通过`@Import`注解导入其他的配置类或者使用`@ComponentScan`注解扫描并注册bean。

```java
public class DependencyLookupDemo {

    public static void main(String[] args) {

        // 配置 XML 信息
        // 启动 Spring 应用上下文
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(Config.class);
        System.out.println("Configs:"+applicationContext.getBeansOfType(Config2.class));

    }
	
    // 在配置类上加上组件扫描注解，等效于在 XML 配置文件中开启扫描注解
    @ComponentScan("tech.fengjian.ioc.container.overview.dependency.lookup")
    public static class Config{

        @Bean
        public User user(){
            User user = new User();
            user.setId(11L);
            user.setName("林黛玉");
            user.setAge(15);
            return user;
        }
    }

    @Component
    public static class Config2{

    }
}
```

@Import 方式

在 Spring 中，当我们使用 `applicationContext.register(Config.class)` 方法手动注册配置类时，该配置类中定义的 Bean 也不会被后续导入的配置类所覆盖。

这是因为，手动注册配置类与通过 `@Import` 注解导入配置类的机制是不同的。手动注册配置类时，Spring 容器会创建一个新的子容器，并将手动注册的配置类放入该子容器中。而子容器中的 Bean 只能被该

子容器中的其他 Bean 或父级容器中的 Bean 所依赖或访问，从而保证了手动注册的配置类中定义的 Bean 不受其他配置类的影响。

这里 Config 中的 User 对象不会被 Import 的对象覆盖，而后续多次 Import 的 Bean，后面的会覆盖前面的。

```java
public class DependencyLookupDemo {

    public static void main(String[] args) {

        // 配置 XML 信息
        // 启动 Spring 应用上下文
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(Config.class);

        System.out.println("Configs:"+applicationContext.getBeansOfType(Config.class));
        System.out.println("Configs:"+applicationContext.getBeansOfType(Config2.class));
        System.out.println("Users:"+applicationContext.getBeansOfType(User.class));

    }

    // Config 中的 User 对象不会被 Import 的对象覆盖
    // 而后续多次 Import 的 Bean，后面的会覆盖前面的
    @Import(value = {Config3.class,Config2.class})
    public static class Config{

        @Bean
        public User user(){
            User user = new User();
            user.setId(11L);
            user.setName("林黛玉");
            user.setAge(15);
            return user;
        }

    }

    public static class Config2{

        @Bean
        public User user(){
            User user = new User();
            user.setId(12L);
            user.setName("薛宝钗");
            user.setAge(16);
            return user;
        }
    }

    public static class Config3{

        @Bean
        public User user(){
            User user = new User();
            user.setId(13L);
            user.setName("凤姐");
            user.setAge(18);
            return user;
        }
    }
}

```



* Java API 配置元信息：
* 命名方式: BeanDefinitionRegistry#registerBeanDefinition(String,BeanDefinition)


```java
public static void main(String[] args) {
    // 创建 BeanFactory 容器
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
    // 启动 Spring 应用上下文
    applicationContext.refresh(); 
    registBeanDefinition(applicationContext, "user", User.class);
    System.out.println(applicationContext.getBean("user"));
    // 显示的关闭 Spring 应用上下文
    applicationContext.close();
}


public static void registBeanDefinition(BeanDefinitionRegistry beanDefinitionRegistry,String beanName,Class<?> beanClass){

    BeanDefinitionBuilder beanDefinitionBuilder = BeanDefinitionBuilder.genericBeanDefinition(beanClass);
    beanDefinitionBuilder.addPropertyValue("id",1);
    beanDefinitionBuilder.addPropertyValue("name","张三");
    beanDefinitionBuilder.addPropertyValue("age",18);

    beanDefinitionRegistry.registerBeanDefinition(beanName,beanDefinitionBuilder.getBeanDefinition());
}
```

* 非命名方式

* BeanDefinitionReaderUtils#registerWithGeneratedName(AbstiractBeanDefinition,BeafinitionRegistry)

```java
public static void main(String[] args) {
    // 创建 BeanFactory 容器
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();

    // 启动 Spring 应用上下文
    applicationContext.refresh();
    registBeanDefinition(applicationContext,User.class);
    System.out.println(applicationContext.getBean(User.class));
    // 显示的关闭 Spring 应用上下文
    applicationContext.close();
}

public static void registBeanDefinition(BeanDefinitionRegistry beanDefinitionRegistry,Class<?> beanClass) {

    BeanDefinitionBuilder beanDefinitionBuilder = BeanDefinitionBuilder.genericBeanDefinition(beanClass);
    beanDefinitionBuilder.addPropertyValue("id", 2);
    beanDefinitionBuilder.addPropertyValue("name", "李四");
    beanDefinitionBuilder.addPropertyValue("age", 18);

    BeanDefinitionReaderUtils.registerWithGeneratedName(beanDefinitionBuilder.getBeanDefinition(),beanDefinitionRegistry);
}
```



* 配置类方式: AnnotatedBeanDefinitionReader#reaister(Class...)

```java
public static void main(String[] args) {
    // 创建 BeanFactory 容器
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
    // 注册 Configuration Class （配置类）
	applicationContext.register(Config.class);
    // 启动 Spring 应用上下文
    applicationContext.refresh();
    // 显示的关闭 Spring 应用上下文
    applicationContext.close();
}
```

## 7. 实例化 Spring Bean

Bean 实例化(Instantiation)

1. 常规方式

* 通过构造器(配置元信息:XML、Java 注解和 Java API)

* 通过静态工厂方法(配置元信息:XML 和 JavaAPI)：静态方法实例化

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user-by-static-method" class="thinking.in.spring.spring.bean.definition.UserFactory"
          factory-method="createUser"/>

</beans>
        
public class UserFactory {

    public static User createUser() {
        User user = new User();
        user.setId(999L);
        user.setName("行者");
        user.setAge(99);
        return user;
    }
}

public class BeanInstantiationDemo {

    public static void main(String[] args) {

        // 加载 XML 配置
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/bean- instantiation-context.xml");

        User user = beanFactory.getBean("user-by-static-method", User.class);
        System.out.println("user:" + user);

    }
}
```

* 通过 Bean 工厂方法(配置元信息:XML 和 JavaAPI)：实例化方法实例化

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user-by-instance-method" class="thinking.in.spring.spring.bean.definition.UserFactory"
          factory-method="createUser" factory-bean="userFactory"/>

    <bean id="userFactory" class="thinking.in.spring.spring.bean.definition.UserFactory"/>
</beans>
        
public class BeanInstantiationDemo {

    public static void main(String[] args) {

        // 加载 XML 配置
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/bean- instantiation-context.xml");

        User user = beanFactory.getBean("user-by-instance-method", User.class);
        System.out.println("user:" + user);

    }
}
```

* 通过 FactoryBean(配置元信息:XML、Java 注解和 Java 注解和 Java/API

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

   <bean id="user-by-factory-bean" class="thinking.in.spring.spring.bean.definition.UserFactoryBean"/>


</beans>

public class BeanInstantiationDemo {

    public static void main(String[] args) {

        // 加载 XML 配置
        // 启动 Spring 应用上下文
        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/bean- instantiation-context.xml");

        User user = beanFactory.getBean("user-by-factory-bean", User.class);
        System.out.println("user:" + user);

    }
}
       
```



2. 特殊方式

* 通过 ServiceLoaderFactoryBean(配置元信息:XML、Java 注解和 JavaAPI)

先观察 ServiceLoader 类

```java
private static final String PREFIX = "META-INF/services/";
```

在 Spring 中可以去适配我们的 ServiceLoader 这个实现，ServiceLoaderFactoryBean，一个实现步骤

​	1）创建目录 `META-INF/services/`

​	2）它通常来说是通过接口的方式来进行创建的，在 services 目录下新建一个没有后缀名的文件，文件名称为接口的全路径

​	3）将接口的所有`实现类`全路径复制到文件中

```java
public static void main(String[] args) {

    demoServiceLoader();
}

public static void demoServiceLoader() {
    ServiceLoader<UserFactory> serviceLoader = ServiceLoader.load(UserFactory.class, Thread.currentThread().getContextClassLoader());
    Iterator<UserFactory> iterator = serviceLoader.iterator();
    while (iterator.hasNext()) {
        UserFactory userFactory = iterator.next();
        System.out.println(userFactory.createUser());
    }
}
```

再看下 ServiceLoaderFactoryBean 如何实现

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userFactoryServiceLoader" class="org.springframework.beans.factory.serviceloader.ServiceLoaderFactoryBean">
        <property name="serviceType" value="thinking.in.spring.spring.bean.definition.UserFactory"/>
    </bean>

</beans>
        
public class SpecialBeanInstantiationDemo {

    public static void main(String[] args) {

        BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath:META-INF/special-bean-instantiation-context.xml");
        ServiceLoader<UserFactory> userFactoryServiceLoader = beanFactory.getBean("userFactoryServiceLoader", ServiceLoader.class);
        displayServiceLoader(userFactoryServiceLoader);
    }

    public static void displayServiceLoader(ServiceLoader<UserFactory> serviceLoader) {
        Iterator<UserFactory> iterator = serviceLoader.iterator();
        while (iterator.hasNext()) {
            UserFactory userFactory = iterator.next();
            System.out.println(userFactory.createUser());
        }
    }
}
```

* 通过 AutowireCapableBeanFactory#createBean(java.lang.Classs, int, boolean)

```java
public static void main(String[] args) {

    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("classpath:META-INF/special-bean-instantiation-context.xml");
    AutowireCapableBeanFactory beanFactory = applicationContext.getAutowireCapableBeanFactory();
    UserFactory userFactory = beanFactory.createBean(DefaultUserFactory.class);
    System.out.println(userFactory.createUser());
}
```

* 通过 BeanDefinitionRegistry#registerBeanDefinition(String,BeanDefinition)

## 8. 初始化 Spring Bean

Bean 初始化(Initialization)

1. @PostConstruct 标注方法

```java
public class DefaultUserFactory implements UserFactory {

    // 1. 基于 @PostConstruct 注解
    @PostConstruct
    public void init() {
        System.out.println("@PostConstruct : UserFactory 初始化中...");
    }
}

public class BeanInitializationDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(BeanInitializationDemo.class);
        applicationContext.refresh();

        applicationContext.getBean(UserFactory.class);
        applicationContext.close();
    }

    @Bean
    public UserFactory userFactory() {
        return new DefaultUserFactory();
    }
}
```

2. 实现 InitializingBean 接口的 afterPropertiesSet() 方法

```java
public class DefaultUserFactory implements UserFactory, InitializingBean {

    // 1. 基于 @PostConstruct 注解
    @PostConstruct
    public void init() {
        System.out.println("@PostConstruct : UserFactory 初始化中...");
    }

    // 2. 基于 InitializingBean 接口的 afterPropertiesSet 非法

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean#afterPropertiesSet() : UserFactory 初始化中...");
    }
}
```



3. 自定义初始化方法

* XML 配置: <bean init-method="init".../>

* Java 注解: @Bean(initMethod="init")

* Java API: AbstractBeanDefinition#setInitMethodName(String)

```java
public class DefaultUserFactory implements UserFactory, InitializingBean {

    // 1. 基于 @PostConstruct 注解
    @PostConstruct
    public void init() {
        System.out.println("@PostConstruct : UserFactory 初始化中...");
    }

    // 2. 基于 InitializingBean 接口的 afterPropertiesSet 非法

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean#afterPropertiesSet() : UserFactory 初始化中...");
    }

    // 3.自定义初始化方法
    public void initUserFactory() {
        System.out.println("自定义初始化方法 initUserFactory() : UserFactory 初始化中...");
    }
}


public class BeanInitializationDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(BeanInitializationDemo.class);
        applicationContext.refresh();

        applicationContext.getBean(UserFactory.class);
        applicationContext.close();
    }

    @Bean(initMethod = "initUserFactory")
    public UserFactory userFactory() {
        return new DefaultUserFactory();
    }
}
```

**思考：**假设以上三种方式均在同一 Bean 中定义,那么这些方法的执行顺序是怎样?

```java
@PostConstruct : UserFactory 初始化中...
InitializingBean#afterPropertiesSet() : UserFactory 初始化中...
自定义初始化方法 initUserFactory() : UserFactory 初始化中...
```



Java API: AbstractBeanDefinition#setInitMethodName(String) 的实现，自定义方法（无论是注解上配置的 initMethod 还是 xml 文件中配置的）本质也是调用这个方法

## 9. 延迟初始化 Spring Bean

Bean 延迟初始化(Lazylnitialization)

XML 配置:<beanlazy-init="true".../>

Java 注解:@Lazy(true)

**思考：**当某个Bean定义为延迟初始化,那么,Spring 容器返回的对象与非延迟的对象存在怎样的差异?

延迟初始化：Spring 应用上下文先启动，然后按需初始化

```java
public class BeanInitializationDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(BeanInitializationDemo.class);
        applicationContext.refresh();
        // 非延迟初始化在 Spring 应用上下文启动完成后被初始化
        System.out.println("Spring 应用上下文已启动...");
        UserFactory userFactory = applicationContext.getBean(UserFactory.class);
        System.out.println("userFactory:" + userFactory);
        applicationContext.close();
    }

    @Lazy
    @Bean(initMethod = "initUserFactory")
    public UserFactory userFactory() {
        return new DefaultUserFactory();
    }
}
```

打印结果：

```java
Spring 应用上下文已启动...
@PostConstruct : UserFactory 初始化中...
InitializingBean#afterPropertiesSet() : UserFactory 初始化中...
自定义初始化方法 initUserFactory() : UserFactory 初始化中...
```

非延迟初始化：Spring 上下文启动完成后被初始化了

```java
public class BeanInitializationDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(BeanInitializationDemo.class);
        applicationContext.refresh();
        // 非延迟初始化在 Spring 应用上下文启动完成后被初始化
        System.out.println("Spring 应用上下文已启动...");
        UserFactory userFactory = applicationContext.getBean(UserFactory.class);
        System.out.println("userFactory:" + userFactory);
        applicationContext.close();
    }

    @Lazy(value = true)
    @Bean(initMethod = "initUserFactory")
    public UserFactory userFactory() {
        return new DefaultUserFactory();
    }
}
```

打印结果：

```java
@PostConstruct : UserFactory 初始化中...
InitializingBean#afterPropertiesSet() : UserFactory 初始化中...
自定义初始化方法 initUserFactory() : UserFactory 初始化中...
Spring 应用上下文已启动...
```

refresh 方法中 finishBeanFactoryInitialization(beanFactory)方法完成 BeanFactory 初始化

```java
// Instantiate all remaining (non-lazy-init) singletons.
finishBeanFactoryInitialization(beanFactory);
```

## 10. 销毁 Spring Bean

Bean销毁(Destroy):close方法触发销毁动作

1. @PreDestroy 标注方法

2. 实现 DisposableBean 接口的destroy()方法

3. 自定义销毁方法

* XML 配置:<beandestroy="destroy".../>

* Java 注解:@Bean(destroy="destroy")

* Java API: AbstractBeanDefinition#setDestroyMethodName(String)

**思考**:假设以上三种方式均在同一Bean中定义,那么这些方法的执行顺序是怎样?

```java
public class DefaultUserFactory implements UserFactory, InitializingBean, DisposableBean {

    // 1. 基于 @PostConstruct 注解
    @PostConstruct
    public void init() {
        System.out.println("@PostConstruct : UserFactory 初始化中...");
    }

    // 2. 基于 InitializingBean 接口的 afterPropertiesSet 非法

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitializingBean#afterPropertiesSet() : UserFactory 初始化中...");
    }

    // 3.自定义初始化方法
    public void initUserFactory() {
        System.out.println("自定义初始化方法 initUserFactory() : UserFactory 初始化中...");
    }

    @PreDestroy
    public void preDestroy() {
        System.out.println("@PreDestroy : UserFactory 销毁中...");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("DisposableBean#destroy() : UserFactory 销毁中...");
    }

    public void destroyUserFactory(){
        System.out.println("自定义初销毁方法 destroyUserFactory() : UserFactory 销毁中...");
    }
}

public class BeanInitializationDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(BeanInitializationDemo.class);
        applicationContext.refresh();
        // 非延迟初始化在 Spring 应用上下文启动完成后被初始化
        System.out.println("Spring 应用上下文已启动...");
        UserFactory userFactory = applicationContext.getBean(UserFactory.class);
        System.out.println("userFactory:" + userFactory);
        applicationContext.close();
    }

    @Lazy(value = false)
    @Bean(initMethod = "initUserFactory",destroyMethod = "destroyUserFactory")
    public UserFactory userFactory() {
        return new DefaultUserFactory();
    }
}
```

打印结果：

```java
@PreDestroy : UserFactory 销毁中...
DisposableBean#destroy() : UserFactory 销毁中...
自定义初销毁方法 destroyUserFactory() : UserFactory 销毁中...
```

## 11. 垃圾回收 Spring Bean

Bean垃圾回收(GC)

1.关闭 Spring 容器(应用上下文)

2.执行 GC

3.SpringBean 覆盖的 finalize()方法被回调

finalize 方法是 Object 类中的方法，不一定每次都会被调用到，不代表对象不会被回收

```java
public class BeanGarbageCollectionDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
        applicationContext.register(BeanInitializationDemo.class);
        applicationContext.refresh();

        applicationContext.close();
        System.gc();

        System.out.println("Spring 应用上下文已关闭");

    }
}


public class DefaultUserFactory implements UserFactory {

    @Override
    protected void finalize() throws Throwable {
        System.out.println("当前对象 DefaultUserFactory 正则被回收...");
    }
}

```



## 12. 面试题

**<font color="green" size="2">沙雕面试题</font>**-如何注册一个 SpringBean?？

答：通过 BeanDefinition 和外部单体对象来注册，BeanDefinition 参照 <a href="#6">注册 Spring Bean</a> 了解，

这里说一下外部单体对象注册，就是相当于说这个对象的生命周期并不由 Spring 容器来进行管理，但是也可以被它托管，写一段代码体会一下：

```java
public class ExternalSingleTonRegistDemo {

    public static void main(String[] args) {

        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();

        // 创建外部对象 userFactory
        UserFactory userFactory = new DefaultUserFactory();
        ConfigurableListableBeanFactory beanFactory = applicationContext.getBeanFactory();
        // 注册外部单例对象
        beanFactory.registerSingleton("userFactory", userFactory);

        applicationContext.refresh();

        // 通过依赖查找获取 UserFactory 对象
        UserFactory userFactoryLookup = beanFactory.getBean("userFactory", UserFactory.class);
        System.out.println("userFactory == userFactoryLookup : " + (userFactory == userFactoryLookup));

        applicationContext.close();

    }
}
```

**<font color="orange" size="2">996面试题</font>**-什么是 Spring BeanDefinition?

答： 回顾"定义 Spring Bean"和"BeanDefinition 元信息"

**<font color="red" size="2">劝退面试题</font>**-Spring 容器是怎样管理注册 Bean？

答:答案将在后续专题章节详细讨论,如:IoC 配置元信息读取和解析、依赖查找和注入以及 Bean 生命周期等。



__本节完__
