---
title: java 泛型
tags: 
    - 泛型
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/code.jpg
# excerpt: 中文乱码了，明明已经使用utf8格式了，为什么还是乱码？
categories:
    - 后端
    - java
---

## 1. 基本概念

* 通常情况下，集合中可以存放不同类型的对象，是因为将所有对象当做 Object 类型放入的，因此从集合中取出元素时也是 Object 类型，为了表达该元素真实的数据类型，则需要强制类型转换，而强制类型转换可能会引发类型转换异常。

  ```java
  List lt1 = new LinkedList();
  
  lt1.add("jack");
  lt1.add(123);
  lt1.add(true);
  
  for (Object o : lt1) {
      String s1 = (String) o;
      System.out.println(s1);
  }
  
  异常提醒：
  java.lang.Integer cannot be cast to java.lang.String
  ```

  

* 为了避免上述错误的发生，从 Java5 开始增加泛型机制，也就是在集合名称的右侧使用`<数据类型>`的方式来明确要求该集合中可以存放的元素类型，若放入其它类型的元素则编译报错。

* 泛型只在编译期有效，在运行时期不区分是什么类型。

## 2. 底层原理

* 泛型的本质就是`参数化类型`，也就是让数据类型作为参数传递，其中 E 相当于形式参数负责占位，而使用集合时`<>`中的数据类型相当于实际参数，用于给形式参数 E 进行初始化，从而使得集合中所有的 E 被实际参数替换，由于实际参数可以传递各种各样广泛的数据类型，因此得名为泛型。

  如：

  ```java
  // 其中 i 叫做形式参数，负责占位
  // int i = 10;
  // int i = 20;
  public void show(int i){
      ...
  }
  // 其中 10 叫做实际参数，负责给形式参数初始化
  show(10);
  show(20);
  ```

  ```java
  // 其中 E 叫做形式参数，负责占位
  // E = String;
  // E = Integer;
  public interface List<E>{
      ...
  }
  // 其中 String 叫做实际参数
  List<String> lt1 = ...;
  List<Integer> lt2 = ...;
  ```

## 3. 自定义泛型接口

泛型接口和普通接口的区别就是后面添加了类型参数列表，可以有多个类型参数，如：<E,T...>等。

## 4. 自定义泛型类

* 泛型类型和普通类型的区别就是类名后面添加了类型参数列表，可以有多个类型参数，如<E,T...>等。

* 实例化泛型类时应该指定具体的数据类型，并且是引用数据类型，而不是基本数据类型

  自定义泛型类：

  ```java
  public class Person<T> {
  
      private String name;
      private int age;
      private T gender;
  
      public Person() {
  
      }
  
      public Person(String name, int age, T gender) {
          this.name = name;
          this.age = age;
          this.gender = gender;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public int getAge() {
          return age;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
  
      public T getGender() {
          return gender;
      }
  
      public void setGender(T gender) {
          this.gender = gender;
      }
  
      @Override
      public String toString() {
          return "Person{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  ", gender=" + gender +
                  '}';
      }
  }
  ```

  使用泛型类：

  ```java
  // 限制类型，gender 被限制为 String 类型
  Person<String> p1 = new Person<>("jack", 18, "男");
  System.out.println(p1);
  
  // 未限制类型的使用，gender 仍被当做 Object 类型
  Person p2 = new Person("jack", 18, "男");
  System.out.println(p2);
  ```

  

* 父类有泛型，子类可以选择保留泛型，也可以指定泛型类型

  ```java
  // 不保留父类的泛型
  public class SubPerson extends Person{
      ...
  }
  
  // 指定父类泛型类型
  public class SubPerson extends Person<String>{
      ...
  }
  
  // 保留父类泛型
  public class SubPerson<T> extends Person<T>{
      ...
  }
  
  ```

  

* 子类除了保留和指定父类的泛型外，还可以增加自己的泛型

  ```java
  // 保留父类泛型的同时，新增自己的泛型
  public class SubPerson<T,K> extends Person<T>{
      ...
  }
  ```


## 5. 自定义泛型方法

* 泛型方法就是我们输入参数的时候，输入的是泛型参数，而不是具体类型的参数。我们在调用这个泛型方法的时候需要对泛型参数进行示例化。

* 泛型方法的格式：

  [访问权限] <泛型> 返回值类型 方法名([泛型标识符 参数名称]){方法体;}

  ```java
  public <T> void printName(T t){
      ...
  }
  
  public <T> String printName(T t){
      ...
      return "xxx";
  }
  ```

  

* 在静态方法中使用泛型参数的时候，需要我们把静态方法定义成泛型方法。

  ```java
  public static <T> void printName(T t){
      ...
  }
  ```

## 6. 泛型在继承上的体现

* 如果 B 是 A 的一个子类或接口，而 G 是具有泛型声明的类或接口，则 G<B> 并不是 G<A> 的子类型！

  比如：String 是 Object 的子类，但 List<String> 并不是 List<Object> 的子类。

  ```java
  List<String> lt1 = new LinkedList<>();
  
  List<Object> lt2 = new LinkedList<>();
  
  lt2 = lt1; // 错误 ×
  
  
  String s1 = "abc";
  
  Object s2 = "1234";
         
  s2 = s1;// 正确 √
  ```

* 需要使用通配符来确定父子类型
  * List<?> 是 List<? extends Number> 的父类型
  * List<? extends Number> 是 List< Integer > 的父类型

## 7. 通配符的使用

* 有时候我们希望传入的类型在一个指定的范围内，此时就可以使用泛型通配符了。

  如：之前传入的类型要求为 Integer 类型，但是后来业务需要 Integer 的父类 Number 类也可以传入

* 泛型通配符有三种形式：（限定存入的类型，根据父类引用指向子类对象，通常当做方法参数使用）

  * <?> 无限制通配符：表示我们可以传入任意类型

    不支持添加元素，因为可以存放任意类型，如果允许添加，则编译器无法知道即将放入的到底是什么类型，可能会出现类型强转异常

    ```java
    List<?> lt5 = new LinkedList<>();
    
    lt5.add("jack");// 错误 ×，
    lt5.get(0);// 正确 √
    ```

  * <? extends E> 表示类型的上界是 E，只能是 E 或者 E 的子类

    不支持添加元素，因为允许的类型是 E 或 E的子类，父类引用可以指向子类，子类不可以指向父类，所以这里面不确定放入的是哪个子类，也可能会出现类型强转异常

    ```java
    List<? extends Person> lt6 = new LinkedList<>();
    lt6.add(new Person());// 错误 ×，这里的 ? 如果是代表 SubPerson
    lt6.add(new SubPerson());// 错误 ×，这里的 ? 如果是 SubPerson 的子类
    lt6.get(0);// 正确 √
    ```

  * <? super E> 表示类型的下界是 E，只能是 E 或 E 的父类
    支持添加元素，这里存入的是 E 或 E 的父类，最小就是 E，那么凡事可以用 E 指向的类型都可以存放（还是父类引用指向子类对象）

    ```java
    List<? super Person> lt7 = new LinkedList<>();
    lt7.add(new Person());// 正确 √，这里的 ? 是 Person 或 Person 父类
    lt7.add(new SubPerson());// 正确 √，这里的 ? 是 Person 或 Person 父类
    ```

    



