---
title: 日志框架之 Logback
tags: 
    - logback
    - 日志框架
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/logback/logback001.jpeg
# excerpt: 最好用的 java 日志框架
---

## 纷杂の日志框架

   日志门面：JCL SLF4J Jboss-Logging

   日志实现：JUL Log4j Log4j2 Logbck

## 真假猴王

SLF4J、Log4j、Logbck 均出自一人之手 `Ceki Gülcü`，而 Log4j2 则是 apache 开发的，Logback 才是真正意义上的 ‘Log4j2’，是 Log4j 的升级版。

## 溯源

> 官网地址：[传送门](https://logback.qos.ch/)

## Logback 构成模块

Logback 项目由三个模块构成：logback-core, logback-classic and logback-access，其中:

* logback-core 是 logback-classic 和 logback-access 的基础
  
* logback-classic 则实现了 SLF4J API，从而可以方便的将日志实现从 Logback 切换为 JUL 或者 Log4j

* logback-access 则整合了 Servlet 容器（比如 Tomcat、Jetty ），提供了 HTTP 方式访问日志功能

## 基础使用

1. 引入依赖 logback-classic.xml
```xml
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.9</version>
  </dependency>
```

    logback-classic 依赖 logback-core 和 slf4j，这里只需要引入 logback-classic 即可

![logback-classic](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/logback/logback003.jpg)

2. 代码中打印日志，通过 slf4j 的 api 进行日志打印，底层日志实现是透明的
```java
package pro.fengjian;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld1 {

    public static void main(String[] args) {

        Logger logger = LoggerFactory.getLogger("pro.fengjian.HelloWorld1");
        logger.debug("Hello world.");

    }
}
```

3. 假设配置文件 logback-test.xml 和 logback.xml 都不存在，那么 logback 默认地会调用BasicConfigurator ， 创 建一 个 最小 化配 置 。最 小化 配置 由 一个 关联 到 根 logger 的ConsoleAppender 组成。
输出用模式为%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n 的 PatternLayoutEncoder 进行格式化。还有，根 logger 默认级别是 DEBUG。

    控制台输出
```java
00:52:06.711 [main] DEBUG pro.fengjian.HelloWorld1 - Hello world.
```

## 名词解释

### Logger

记录器，日志记录的实际执行者，self4j api, 实现于 logback-classic 模块
```java
public interface Logger {

  // Printing methods: 
  public void trace(String message);
  public void debug(String message);
  public void info(String message); 
  public void warn(String message); 
  public void error(String message); 
}
```

Logger 名称区分大小写，并遵循分层命名规则：比如 com.foo 是 com.foo.bar 的父类

顶级的 Logger 名为：`ROOT`，类似于 Java 的 Object，所有的 Logger 都继承顶级 Logger

Logger 的获取：
```java
Logger x = LoggerFactory.getLogger("wombat"); 
Logger y = LoggerFactory.getLogger("wombat");
```
x,y 是同一个 Logger 对象，通常使用 Logger 所在类的全限定名称来定义一个 Logger

### Logger context

上下文，将不同 Logger 限定在自己的空间内，达到隔离的效果，从而实现某些 Logger 的记录和阻止某些 Logger 的记录

### Logger level

级别，定义于 `ch.qos.logback.classic.Level ` 类，Logger 在定义的同时会被分配相应的级别，级别是有继承关系的，低等级生效的同时，它继承的高级别也会生效
目前 Logger 的级别有：

从低到高： TRACE < DEBUG < INFO <  WARN < ERROR

顶级 Logger 的级别默认为 DEBUG
如果一个 Logger 没有被分配 Level，那最终凭借继承关系，继承其父 Logger 的 Level，比如 x.y 的 Logger 继承 x 的 Level
如果没有任何的父 Logger，它将被分配跟顶级Logger 一样的级别 DEBUG

### Appender

附加器 / 添加器，定义于 `ch.qos.logback.core.Appender` 类，将 Logger 记录的信息输出到指定的位置（比如 控制台、文件、关系型数据库等），一个 Logger 可以对应多个 Appender

Appender 也具有继承性，Appender 的 Additivity（相加性） 属性默认为 true， 即个 Appender 会将日志输出到自己指定的位置的同时，也会输出到它继承的父类所指定的 Appender

![appender结构图](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/logback/logback004.png)

* ConsoleAppender
  
    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>

    <configuration>
    <import class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"/>
    <import class="ch.qos.logback.core.ConsoleAppender"/>

    <appender name="STDOUT" class="ConsoleAppender">
        <encoder class="PatternLayoutEncoder">
        <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="STDOUT"/>
    </root>
    </configuration>
    ```

    <font color="red">提示:</font>若`import`不好使，直接使用全路径即可

* FileAppender

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>

    <configuration>
    <import class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"/>
    <import class="ch.qos.logback.core.FileAppender"/>

    <appender name="FILE" class="FileAppender">
        <file>testFile.log</file>
        <append>true</append>
        <immediateFlush>true</immediateFlush>
        <encoder class="PatternLayoutEncoder">
        <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="FILE"/>
    </root>
    </configuration>
    ```

    使用时间定义唯一文件名

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>

    <configuration>
    <import class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"/>
    <import class="ch.qos.logback.core.FileAppender"/>
    <timestamp name="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>

    <appender name="FILE" class="FileAppender">
        <file>log-${bySecond}.txt</file>
        <encoder class="PatternLayoutEncoder">
        <pattern>%logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="FILE"/>
    </root>
    </configuration>
    ```

* RollingFileAppender

    TimeBasedRollingPolicy: 基于时间的滚动策略

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>

    <configuration>
    <import class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"/>
    <import class="ch.qos.logback.core.rolling.RollingFileAppender"/>
    <import class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy"/>

    <appender name="FILE" class="RollingFileAppender">
        <file>logFile.log</file>
        <rollingPolicy class="TimeBasedRollingPolicy">
        <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>
        <maxHistory>30</maxHistory>
        <totalSizeCap>3GB</totalSizeCap>
        </rollingPolicy>
        <encoder class="PatternLayoutEncoder">
        <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="FILE"/>
    </root>
    </configuration>
    ```

    SizeAndTimeBasedRollingPolicy: 基于时间和空间的滚动策略

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>

    <configuration>
    <import class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"/>
    <import class="ch.qos.logback.core.rolling.RollingFileAppender"/>
    <import class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy"/>

    <appender name="ROLLING" class="RollingFileAppender">
        <file>mylog.txt</file>
        <rollingPolicy class="SizeAndTimeBasedRollingPolicy">
        <fileNamePattern>mylog-%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
        <maxFileSize>100MB</maxFileSize>
        <maxHistory>60</maxHistory>
        <totalSizeCap>20GB</totalSizeCap>
        </rollingPolicy>
        <encoder class="PatternLayoutEncoder">
        <pattern>%msg%n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="ROLLING"/>
    </root>
    </configuration>
    ```

* FixedWindowRollingPolicy： 固定窗口滚动策略

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>

    <configuration>
    <import class="ch.qos.logback.classic.encoder.PatternLayoutEncoder"/>
    <import class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy"/>
    <import class="ch.qos.logback.core.rolling.RollingFileAppender"/>
    <import class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy"/>

    <appender name="FILE" class="RollingFileAppender">
        <file>test.log</file>
        <rollingPolicy class="FixedWindowRollingPolicy">
        <fileNamePattern>tests.%i.log.zip</fileNamePattern>
        <minIndex>1</minIndex>
        <maxIndex>3</maxIndex>
        </rollingPolicy>
        <triggeringPolicy class="SizeBasedTriggeringPolicy">
        <maxFileSize>5MB</maxFileSize>
        </triggeringPolicy>
        <encoder class="PatternLayoutEncoder">
        <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="DEBUG">
        <appender-ref ref="FILE"/>
    </root>
    </configuration>
    ```

### Layout && Encoder

布局和编码器，将日志信息按照指定的格式输出,从 logback 0.9.19 开始， FileAppender子类需要一个编码器，不再采用 layout。

PatternLayout：C 语言风格的输出方式

PatternLayoutEncoder：只是包装了 a PatternLayout

比如 %-4relative [%thread] %-5level %logger{32} - %msg%n
```java
176  [main] DEBUG manual.architecture.HelloWorld2 - Hello world.
```

指定输出颜色：%blue(xxx)

打印自应用程序启动以来经过的时间、日志记录事件的级别、括号之间的调用者线程、它的记录器名称、一个破折号，后面是事件消息和一条新线

### Filter

Logback 过滤器基于三元逻辑，允许将它们组装或链接在一起以构成任意复杂的过滤策略。它们很大程度上受到 Linux 的 iptables 的启发

* 自定义过滤器
  
    ```java
    package chapters.filters;

    import ch.qos.logback.classic.spi.ILoggingEvent;
    import ch.qos.logback.core.filter.Filter;
    import ch.qos.logback.core.spi.FilterReply;

    public class SampleFilter extends Filter<ILoggingEvent> {

    @Override
    public FilterReply decide(ILoggingEvent event) {    
        if (event.getMessage().contains("sample")) {
        return FilterReply.ACCEPT;
        } else {
        return FilterReply.NEUTRAL;
        }
    }
    }
    ```

    配置自定义过滤器

    ```xml
    <configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">

        <filter class="chapters.filters.SampleFilter" />

        <encoder>
        <pattern>
            %-4relative [%thread] %-5level %logger - %msg%n
        </pattern>
        </encoder>
    </appender>
            
    <root>
        <appender-ref ref="STDOUT" />
    </root>
    </configuration>
    ```

* LevelFilter

  LevelFilter根据精确级别匹配过滤事件。如果事件的级别等于配置的级别，则过滤器接受或拒绝该事件，具体取决于onMatch和onMismatch属性的配置。
  
  ```xml
  <configuration>
  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
      <level>INFO</level>
      <onMatch>ACCEPT</onMatch>
      <onMismatch>DENY</onMismatch>
    </filter>
    <encoder>
      <pattern>
        %-4relative [%thread] %-5level %logger{30} - %msg%n
      </pattern>
    </encoder>
  </appender>
  <root level="DEBUG">
    <appender-ref ref="CONSOLE" />
  </root>
  </configuration>
  ```

* ThresholdFilter
  
  过滤低于指定阈值的事件。对于级别等于或高于阈值的事件，在调用其 () 方法时将响应 NEUTRAL 。但是，级别低于阈值的事件将被拒绝
  
  ```xml
  <configuration>
    <appender name="CONSOLE"
        class="ch.qos.logback.core.ConsoleAppender">
        <!-- deny all events with a level below INFO, that is TRACE and DEBUG -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <level>INFO</level>
        </filter>
        <encoder>
        <pattern>
            %-4relative [%thread] %-5level %logger{30} - %msg%n
        </pattern>
        </encoder>
    </appender>
    <root level="DEBUG">
        <appender-ref ref="CONSOLE" />
    </root>
  </configuration>
  ```

    <font color="red">提示：</font>配置文件中会根据 Filter 的先后顺序进行过滤

## 关于配置

![配置文件语法](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/logback/logback002.png)

Logback 采取下面的步骤进行自我配置：

1. Logback 尝试在 classpath中找到一个名为 logback-test.xml 的文件。

2. 如果没有找到这样的文件，它会检查 类路径中的文件logback.xml ..

3. 如果没有找到这样的文件，则 使用服务提供者加载工具（在 JDK 1.6 中引入） 通过查找文件 META-INF\services\ch.qos.logback.classic.spi.Configurator来解析接口 的实现类路径。其内容应指定所需 实现的完全限定类名。 com.qos.logback.classic.spi.ConfiguratorConfigurator

4. 如果以上都没有成功，logback 会自动配置自己，BasicConfigurator 这将导致日志输出被定向到控制台。

如果您使用 Maven，并且将 logback-test.xml放在src/test/resources 文件夹下，Maven 将确保它不会包含在生成的工件中。因此，您可以在测试期间使用不同的配置文件，即logback-test.xml，而在生产中使用另一个文件，即logback.xml。

开发环境使用 logback-test.xml， 生产环境使用 logback.xml

BaseConfigurator 的最小配置 `输出使用 PatternLayoutEncoder模式 %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n`
```xml
<configuration>

  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- encoders are assigned the type
         ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

## 最佳实践

### 日志拼接

bad：

```java
logger.debug("Entry number: " + i + " is " + String.valueOf(entry[i]));
```

good:

```java
logger.debug("The entry is {}.", entry);
```

### lombok 的日志注解

bad

```java
package pro.fengjian;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld1 {

    public static void main(String[] args) {

        Logger logger = LoggerFactory.getLogger("pro.fengjian.HelloWorld1");
        logger.debug("Hello world.");

    }
}
```

good

```java
package pro.fengjian;

import lombok.extern.slf4j.Slf4j;
@Slf4j
public class HelloWorld1 {

    public static void main(String[] args) {

        log.debug("Hello,World");
    }
}
```

### Logback in SpringBoot

* application.properties 中配置
  
  ```properties
  logging.pattern.console=%d - %m%n
  #logging.file.path=log
  logging.file.name=log/init.log
  logging.level.pro.fengjian=info
  ```

* logback-spring.xml 中配置(推荐)
  
  日志文件名定义为 logback-spring.xml，spring boot会默认加载此文件，为什么不配置logback.xml,因为logback.xml会先application.properties 加载，而 logback-spring.xml 会后于application.properties 加载，这样我们在 application.properties 中设置日志文件名称和文件路径才能生效。

* 日志要求

  区分 info 和 error 日志，不同级别输出到不同文件
  
  每天（或者其他指定时间）产生一个日志文件

  ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration>
    <configuration>
        <!--输出到控制台-->
        <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
            <!--输出格式-->
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>%d - %m%n</pattern>
            </encoder>
        </appender>

        <!--输出到文件-->
        <appender name="fileInfoLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <!--级别过滤-->
            <filter class="ch.qos.logback.classic.filter.LevelFilter">
                <level>ERROR</level>
                <onMatch>DENY</onMatch>
                <onMismatch>ACCEPT</onMismatch>
            </filter>
            <!--滚动策略-->
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <!--文件名规则-->
                <fileNamePattern>info.%d{yyyy-MM-dd}.log</fileNamePattern>
            </rollingPolicy>
            <!--输出格式-->
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>%d - %m%n</pattern>
            </encoder>
        </appender>

        <!--输出到文件-->
        <appender name="fileErrorLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <!--阈值过滤-->
            <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
                <level>ERROR</level>
            </filter>
            <!--滚动策略-->
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <!--文件名规则-->
                <fileNamePattern>error.%d{yyyy-MM-dd}.log</fileNamePattern>
            </rollingPolicy>
            <!--输出格式-->
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>%d - %m%n</pattern>
            </encoder>
        </appender>

        <!--root Logger-->
        <root level="info">
            <appender-ref ref="consoleLog"/>
            <appender-ref ref="fileInfoLog"/>
            <appender-ref ref="fileErrorLog"/>
        </root>
    </configuration>
  ```

__完__
