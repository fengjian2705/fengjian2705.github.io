---
title: JDK 动态代理
tags: 
    - 动态代理
    - Proxy
    - InvocationHandler
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/proxy/proxy01.jpg
# excerpt: 最好用的 java 日志框架
---

> 基于接口实现，代理类和被代理类实现共同的接口

## 先来看一个静态代理的例子

顾客到饭店吃饭，厨师负责做菜，那么就需要有人来帮厨师获取顾客点的菜，还有上菜

**饭店**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 10:13
 */
package jdk.proxy;

public interface Restaurant {

    void cooking();
}

```

**厨师**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 10:10
 */
package jdk.proxy;

public class Cook implements Restaurant{

    @Override
    public void cooking() {
        System.out.println("我是厨师：我负责做菜");
    }
}

```

**厨师代理**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 10:21
 */
package jdk.proxy;

public class CookProxy implements Restaurant{

    @Override
    public void cooking() {

        // 顾客点菜
        System.out.println("我是服务员：顾客点菜");

        Cook cook = new Cook();
        cook.cooking();

        // 上菜
        System.out.println("我是服务员：上菜");

    }
}

```

**测试**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 10:26
 */
package jdk.proxy;

public class TestCookProxy {

    public static void main(String[] args) {
        CookProxy cookProxy = new CookProxy();
        cookProxy.cooking();
    }
}

```

**静态代理的缺点**

1. 因为代理类和被代理类实现相同的接口，所以代理类必须实现接口的全部方法，会出现`冗余`代码

2. 每个代理类只能针对实现特定接口的被代理类，一个接口一个代理类

3. 多个被代理类，则仍会出现`冗余`代码

## JDK 动态代理

利用 `Proxy` 创建代理类对象（被代理的类一定要实现某个接口），代理类通过实现 `InvocationHandler` 接口的增强器，通过执行 `invoke` 方法来增强被代理的方法

**厨师**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 10:10
 */
package jdk.proxy;

public class Cook implements Restaurant{

    @Override
    public void cooking() {
        System.out.println("我是厨师：我负责做菜");
    }
}

```

**厨师代理类处理器**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 16:22
 */
package jdk.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class CookInvocationHandler implements InvocationHandler {

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        System.out.println("点菜");

        // 被代理类对象
        Cook cook = new Cook();
        method.invoke(cook,args);

        System.out.println("上菜");
        return null;
    }
}

```

**测试代理类**

```java
/**
 * @作者 风间
 * @创建时间 2022/4/9 16:27
 */
package jdk.proxy;

import java.lang.reflect.Proxy;

public class TestCookProxyInvocation {

    public static void main(String[] args) {

        Cook cook = new Cook();

        Restaurant restaurantProxy = (Restaurant)Proxy.newProxyInstance(cook.getClass().getClassLoader(), cook.getClass().getInterfaces(), new CookInvocationHandler());
        restaurantProxy.cooking();
    }
}

```

**newProxyInstance 方法：**

参数1：被代理类的类类加载器
参数2：被代理类实现的接口列表
参数3：实现 `InvocationHanler` 接口的调用处理器，代理类增强方法实际用功处


__完__