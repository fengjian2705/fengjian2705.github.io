---
title: spring
tags:
  - spring
index_img: https://s3.bmp.ovh/imgs/2022/12/13/09030a142dab744d.jpg
# excerpt: spring
categories:
  - 后端
  - spring
---

# spring

1. 当一个类中既有构造方法，又有 set 方法时，依赖注入会失败，因为 spring 优先使用构造方法实现 DI

   ```java
   public class UserServiceImpl implements IUserService {
   
       private IUserDao userDao;
   
       // 构造方法的存在会导致注入失败
       public UserServiceImpl(IUserDao userDao) {
           this.userDao = userDao;
       }
   
       public void setUserDao(IUserDao userDao){
           this.userDao = userDao;
       }
   
       @Override
       public void save() {
           this.userDao.save();
       }
   }
   ```

   ```xml
      <bean id="userService" class="tech.fengjian.service.impl.UserServiceImpl">
           <property name="userDao" ref="userDao"/>
       </bean>
   ```

   

2. spring整合junit4

   * 引入依赖

     ```xml
     <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-test</artifactId>
         <version>5.1.5.RELEASE</version>
     </dependency>
     <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.12</version>
     </dependency>
     ```

     

   * 使用注解

     ```java
     @RunWith(SpringJUnit4ClassRunner.class)// 指定运行器
     @ContextConfiguration(classes = SpringConfig.class)// 指定spring容器
     public class AccountServiceTest {
         
         @Autowired
         private AccountService accountService;
     
         @Test
         public void testSave() {
             ...
         }
     }
     ```

     

3. 动态代理

   * jdk 动态代理

     基于接口

     ```java
     /**
      * JDK动态代理工厂类
      */
     @Component
     public class JDKProxyFactory {
     
         @Autowired
         private AccountService accountService;
     
         /**
          * 采用动态代理技术来生成目标类的代理对象
          * ClassLoader loader：类加载器，借助被代理对象获取类加载器
          * Class<?>[] interfaces：被代理类所需实现的全部接口
          * InvocationHandler h：当代理对象调用接口中的任意方法时，那么会执行 InvocationHandler中的invoke方法
          */
         public AccountService createAccountServiceJDKProxy() {
     
             AccountService accountServiceProxy = (AccountService) Proxy.newProxyInstance(accountService.getClass().getClassLoader(), accountService.getClass().getInterfaces(), new InvocationHandler() {
                 /*
                  * proxy：当前代理对象的引用
                  * method：被调用的目标方法的引用
                  * args：被调用的目标方法所用到的参数
                  */
                 @Override
                 public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                     // 被代理对象的原方法执行
                     System.out.println("目标方法执行之前...");
                     method.invoke(accountService, args);
                     System.out.println("目标方法执行之后...");
                     return null;
                 }
             });
             return accountServiceProxy;
         }
     
     
     }
     ```

     

   * cglib 动态代理

     基于父类
     
     ```java
     /**
      * 该类就是采用cglib动态代理来对目标类（AccountServiceImpl）进行方法（transfer）的动态增强（添加上事务控制）
      */
     @Component
     public class CglibProxyFactory {
     
         @Autowired
         private AccountService accountService;
     
         public AccountService createAccountServiceCglibProxy() {
     
             /**
              * 参数1：目标类的字节码对象
              * 参数2：动作类，当代理对象调用目标对象中的原方法时，那么会执行intercept方法
              */
             AccountService accountServiceProxy = (AccountService) Enhancer.create(accountService.getClass(), new MethodInterceptor() {
                 /*
                  * o：代表生成的代理对象 method：调用目标方法的引用 objects：方法入参 methodProxy：代理方法
                  */
                 @Override
                 public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
     
                     method.invoke(accountService, objects);
     
                     return null;
                 }
             });
             return accountServiceProxy;
         }
     
     }
     ```
     
     
   
   ![5fce0adb0936fe2c05400304](C:/Users/zhulu/Desktop/imgurl图床/5fce0adb0936fe2c05400304.png)