---
title: Python 基础语法
tags:
- python
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/python/python01.jpg
# excerpt: 人生苦短，我用 python
categories:
- 后端
- python
---

## 1. Python多版本问题

### 1.1 环境搭建概览

![](https://s3.bmp.ovh/imgs/2023/01/13/8b2a906664642955.png)

### 1.2 python下载安装

1. 下载最新稳定版本 [官网地址](https://www.python.org/)

2. 执行安装程序，选择自定义安装`Customize installation`，并勾选`Add python 3.11 to PATH`，下一步

   ![](https://s3.bmp.ovh/imgs/2023/01/13/f796dff389b50611.png)

3. Optional Features 保持默认勾选即可，至少保证`pip`勾选上，下一步

   ![](https://s3.bmp.ovh/imgs/2023/01/13/5eee56687230dc49.png)

4. Advanced Options 保持默认勾选即可，可以更改安装位置，下一步

   ![](https://s3.bmp.ovh/imgs/2023/01/13/c2e50667c17b9705.png)

5. 安装成功 [官网文档](https://docs.python.org/3.11/index.html)

   ![](https://s3.bmp.ovh/imgs/2023/01/13/aceed629d18b1c56.png)

### 1.3 检验安装是否成功

`python -V` 命令行显示：Python 3.11.1

`pip -V` 命令行显示：pip 22.3.1

### 1.4 使用pipenv安装虚拟环境

1. 切换到桌面

    ```shell
        cd Desktop
    ```
2. 创建文件夹 fisher，并进入文件夹

    ```shell
        mkdir fisher
        cd fisher
    ```

3. 全局安装pipenv，任意目录

    ```shell
        pip install pipenv
    ```
4. 和项目绑定，给每一个项目创建pipenv

    ```shell
        pipenv install
    ```
5. 启动pipenv进入虚拟环境

    ```shell
        pipenv shell
    ```
6. 查看虚拟环境安装的包

    ```shell
        pip list
    ```

7. pipenv常用命令 [官方文档](https://github.com/pypa/pipenv)

    * exit：退出虚拟环境
    * pipenv shell：进入虚拟环境
    * pipenv install flask：安装包
    * pipenv uninstall flask：卸载包
    * pipenv graph：项目依赖关系图

### 1.5 使用pipenv安装flask包

```shell
    pipenv install flask
```

### 1.6 IDLE与第一段Python代码

搜索 IDLE 命令程序，打开对应版本的 IDLE

```shell
    print('Hello,World')
    Hello,World
```
`提示：`
* python通常使用缩进来控制代码格式
* ;{}可以不用使用

## 2. Python 基本类型

### 2.1 Number 数字

1. 分类

    * 整数：int
    * 浮点数：float

      其它语言：单精度（float），双精度（double）
      其它语言：short，int，long

    * bool 布尔类型：表示真假 True False，注意首字母大写
      对于数字非0即为True
      对于字符串非空即为True
      列表、字典...
      `None` false
    * complex: 复数 36j

2. 代码示例

    ```shell
        // 整数
        type(1)
        <class 'int'>
        type(-1)
        <class 'int'>
      
        // 浮点数
        type(1.1)
        <class 'float'>
        type(1.1111111)
        <class 'float'>
        
        // 加法
        type(1+1.2)
        <class 'float'>
        type(1+1.0)
        <class 'float'>
        type(1+1)
        <class 'int'>
        
        // 乘法
        type(1*1.0)
        <class 'float'>
        type(1*1)
        <class 'int'>
        
        // 除法,得到浮点数
        type(2/2)
        <class 'float'>
        // 整除,得到整数
        type(2//2)
        <class 'int'>
      
        type(True)
        <class 'bool'>
        type(False)
        <class 'bool'>
        int(True)
        1
        int(False)
        0
        bool(0)
        False
        bool(1)
        True
    ```
### 2.2 10、2、8、16进制

1. 10在各进中代表的数字

    * 2进制：0b10 10进制  2
    * 8进制：0o10 10进制  8
    * 16进制：0x10 10进制 16
    * 10进制：10

2. 进制间数字转换

    * 其他进制 -> 2进制：bin
      bin(10)  -> 0b1010
      bin(0o7) -> 0b111
      bin(0xE) -> 0b1110

    * 其他进制 -> 10进制：int
      int(0b111) -> 7
      int(0o77)  -> 63

    * 其他进制 -> 16进制：hex
      hex(888)     -> 0x378
      hex(0o7777)  -> 0xfff

    * 其它进制 -> 8进制:oct
      oct(0b111)  -> 0o7
      oct(0x777)  -> 0o3567

### 2.3  字符串 str

1. 单引号，双引号，三引号

   ```shell
       type('1')
       <class 'str'>
       "let's go" = 'let\'s go'
   ```

2. 多行字符串（三引号）

   python 推荐每行宽度最大 79，超出换行,使用```包围字符串，字符串可以任意换行
   ```shell
      """hello
      """
      'hello\n'
       print("""hello
             """)
       'hello'
   ```
   使用 print 可以去除 `\n`

3. 转义字符

    * 无法看见的字符
      \n换行 \t制表 \r回车

    * 与语言本身语法有冲突的字符
      \' 单引号冲突

   ```shell
       print('hello \n world')
       hello
       world
   ```
   如何输出`\n`而不换行？将`\n`进行转义

   实际意义：
   ```shell
        print('c:\northwind\northwest')
        c:
        orthwind
        orthwest
   ```
   ```shell
        print('c:\\northwind\\northwest')
        c:\\northwind\\northwest
   ```

4. 原始字符串

   ```shell
       print(r'hello \n world')
       hello \n world
   ```
   字符串前面加子母`r`

5. 字符串运算

    * 拼接
       ```shell
           // 加法
          'hello'+'world'
          'helloworld'
          
           // 乘法
           'hello'*3
           'hellohellohello'
       ```
    * 截取 []
       ```shell
           // 正数代表序号
           'hello'[0]
           'h'
           // 负数代表从字符串末尾数n次得到的字符
           'hello'[-1]
           'o'
           // 从第一个字符截取到第个五字符
           'hello world'[0:5]
           'hello'
           // 从第一个字符截取到倒数第二个字符
           'hello world'[0:-1]
           'hello worl'
           // 从第六个字符截取到最后一个字符
           'hello world'[6:]
           'world'
           // 从第一个字符截取到倒数第五个字符
           'hello world'[:-4]
           'hello world'
           // 从末尾往前数四个字符截取出来
           'hello world'[-4:]
           'orld'
       ```


### 2.4 列表

1. 定义

   ```shell
     type([1,2,3,4,5,6])
     <class 'list'>
   ```

2. 特性

    * 内部元素类型不固定，字符串、数字、bool、列表...
      [1,'hello',True,[1,2]]

3. 基本操作

    * 截取（参考字符串截取）
   ```shell
      ['新月打击','苍白之瀑','月之降临','月神冲刺'][0]
      '新月打击'
   ```

    * 列表相加
   ```shell
      ['新月打击','苍白之瀑','月之降临','月神冲刺']+['月神冲刺']
      ['新月打击', '苍白之瀑', '月之降临', '月神冲刺', '月神冲刺']
   ```

    * 列表乘法
   ```shell
      ['新月打击', '苍白之瀑', '月之降临', '月神冲刺', '月神冲刺']*2
      ['新月打击', '苍白之瀑', '月之降临', '月神冲刺', '月神冲刺', '新月打击', '苍白之瀑', '月之降临', '月神冲刺', '月神冲刺']
   ```

   列表减不支持法！

### 2.5 元组

1. 引子（以世界杯分组为例，8个组 每组4个队）

   使用嵌套列表
   ```shell
      [['巴西','克罗地亚','墨西哥','喀麦隆'],[],[],[],[],[],[],[],[]]
   ```

2. 元组定义

   形如`(1,2,3)`、`(1,2,True)`
   ```shell
      type((1,2,3))
      <class 'tuple'>
   ```

3. 元组操作

    * 截取（参考字符串截取）

   特殊情况，一个元素的元组：原因是括号被当做了运算符，后面再加一个,即可
   ```shell
      type((1))
      <class 'int'>
   
      type(('1'))
      <class 'str'>
   
      type((1,))
      <class 'tuple'>

   ```

### 2.6 序列总结 `str list tuple`

1. 序号
   ```shell
      'hello world'[0]
      [1,2,3][0]
      (1,2,3)[0]
   ```

2. 切片
   ```shell
      'hello world'[0:3]
      [1,2,3][0:]
      (1,2,3,4)[-1:]
   ```
3. 加法、乘法

4. 判断元素是否存在 `in` `not in`
   ```shell
      3 in [1,2,3]
      
   ```
5. 长度
   ```shell
      len('hello')
   ```

6. 最大值最小值 `max min`
   ```shell
      max((1,2,3))
      max('hello')
   ```
   字母与 `ascll码`
   ```shell
      ord('w')
      119
   ```

### 2.7 集合set

1. 特点：`无序` `不重复`
2. 定义：形如 {1,2,3,4,5,6} 使用 `{}`包裹元素
3. 长度：len({1,2,3})
4. 是否存在：`in` `not in`
5. 差集
   ```shell
      {1,2,3,4}-{1,3}
      {2,4}
   ```
   交集
   ```shell
      {1,2,3}&{2,3}
      {2, 3}
   ```
   合集
   ```shell
      {1,2,3,4}|{3,4,9}
      {1, 2, 3, 4, 9}
   ```
6. 空集合

   ```shell
      // 字典
      type({})
      <class 'dict'>
      // 空集合
      type(set())
      <class 'set'>
   ```

### 2.8 字典dict

1. 定义：key value 构成
   ```shell
      type({'name':'jack','age':18})
      <class 'dict'>
   ```
   其他语言：map

2. 特点：

    * key 不重复
    * key 类型可以为 str number
    * 字典中元素还可以是字典
    * key 必须是不可变的类型 int str tuple

3. 操作

    * 根据 key 获取 value
      ```shell
         {'name':'jack','age':18}['name']
         'jack'
      ```

4. 空字典： {}

### 2.9 基本数据类型总结

![python基本数据类型](https://s3.bmp.ovh/imgs/2023/01/14/11ca061bfad3d87c.png)

## 3. 变量与运算符

### 3.1 变量

1. 什么是变量？

   变量就是一个名字，代表定义的数据： A = [1,2,4]， B = 'hello world'，C = {'name':'jack'}

2. 变量命名规则

    * 首字符不能数字
    * 只可以使用字母、数字、下划线
    * 系统关键字（保留关键字）不能用于变量名：and if import ...
    * 区分大小写

3. 值类型与引用类型

   值类型：int str tuple（不可改变）
   引用类型：list set dict（可变）

   ```shell
      a=1
      b=a
      a=3
      print(b)
      1
   
      a = [1,2,3]
      b = a
      a[0] = '1'
      print(b)
      ['1', 2, 3]

    ```

   问题：字符串不可变，那下面 a 为何可以进行计算？
   a = 'hello'
   a = a + 'python'
   print(a)
   hellopython

   计算后的a已经不是原来的字符串了！通过 id 函数查看！
   b = 'hello'
   id(b)
   2330237640816
   b = b + 'python'
   id(b)
   2330237667696

4. 列表的可变与元组的不可变

   a = [1,2,3]
   id(a)
   2330237710656
   a[0] = '1'
   id(a)
   2330237710656

   a = (1,2,3)
   id(a)
   2330237466624
   a[0] = '1'
   Traceback (most recent call last):
   File "<pyshell#25>", line 1, in <module>
   a[0] = '1'
   TypeError: 'tuple' object does not support item assignment

### 3.2 运算符号

![](https://s3.bmp.ovh/imgs/2023/01/15/ae5b4ad3fd992412.png)

* 成员运算符
  a = 1
  a in [1,2,3,4,5]
  True

  b = 6
  b in [1,2,3,4,5]
  False

  b = 'h'
  b in 'hello'
  True

  b not in (1,2,3,4,5)
  True

  b not in {1,2,3,4,5}

* 比较运算符
  1 and 2
  2
  2 and 1
  1
  1 and 0
  0
  1 or 2
  1
  1 or 0
  1
  0 or 1
  1
  b = 'a'
  b in {'c':1}

* 身份运算符
  is 比较的是两个变量身份是否相等，内存地址
  == 是比较两个变量取值是否相等
  a = 1.0
  b = 1
  a ==b
  True
  a is b
  False

  a = [1,2,3]
  b = [3,2,1]
  a == b
  True
  a is b
  False

  a = (1,2,3)
  b = (2,1,3)
  a ==b
  False
  a is b
  False

* 类型判断
  a = 'k'
  isinstance(a,str)
  True

  isinstance(a,(int,str,float))

* 对象的三个特征：id value type
  比较方式：
  vlaue : ==
  id: is
  type: isinstance

* 位运算符：把一个数字当做二进制数进行运算
    * `&` 按位与
  ```shell
     a = 2
     b = 3
     a & b = 2
  ```
    * `|` 按位或
       ```shell
       a = 2
       b = 3
       a | b = 3
  ```
  * `^` 按位异或
  * `<<` 左移动
  * `>>`右移动

## 4. 分支、循环、条件与枚举

### 4.1 表达式

1.  概念
    表达式（Expression）是运算符（operator）和操作数（operand）所构成的序列，形如 1 + 1 、a = [1,2,3] 、a > b

2.  表达式的优先级
    ```shell
        a = 1
        b = 2
        c = 3
        
        a + b * c 
        7
        
        a or b and c
        1
    ```
    ![python运算符优先级](https://s3.bmp.ovh/imgs/2023/01/16/5bdb8b465a3aee61.png)

    `提示：` 推荐使用括号来表达你期望的优先级！

3.  在文本文件中编写 python：vscode

### 4.2 条件控制 if elif else

* snippet 片段,tab 自动切换
  ```shell
      if condition:
          pass
      elif condition:
          pass
      else:
          pass
  ```

### 4.3 循环 while

* while
  ```shell
     while condition:
         pass
  ```

* while + else
  ```shell
     while condition:
       pass
     else:
       pass
  ```

### 4.4 循环 for

* for
  ```shell
     for target_list in expression_list:
       pass
  ```
* for + else
  ```shell
     for target_list in expression_list:
       pass
     else:
       pass
  ```
  `提示：` else 表示 for 循环执行完毕后执行的语句，使用 break 终止则 else 后的语句不执行，continue 则会执行！
* for 与 range
  for 类似于其它语言的 for-each
  range 则类似于 fori，range(start,end,step)
  ```shell
     a = [1,2,3,4,5,6,7,8]
     for i in range(1,len(a),2):
     print(a[i],end=' | ')
  ```

## 5. 包、模块、函数与变量作用域

### 5.1 python 工程的组织结构

1. 包：顶级结构，简单理解为一个文件夹（物理表现）

    * 包在表现形式上是文件夹，文件夹 + `__init__.py` = 包
    * `__init__.py`也是一个模块，它的名字就是这个模块所在的包的包名

2. 模块：第二层，可以理解为文件夹下的文件（物理表现）

3. 类：第三层

4. 函数、变量：并不归属于组织结构，属于类的特性

### 5.2 import 导入模块

1. c7.py
    ```shell
       a = 2
       
    ```

2. c8.py
   ```shell
      import c7
   
      print(c7.a)
   ```

3. import 只能导入模块
   解决引用路径过长问题：import t.c7 as m

### 5.3 from import 导入变量

1. 语法
    ```shell
        from t.c7 import a
        print(a)
    ```
2. 导入模块，等价于 import 导入模式
    ```shell
        from t import c7
        print(c7.a)
    ```
3. 导入多个变量
    ```shell
        from t.c7 import *
        print(a)
        print(b)
        print(c)

    ```
    `提示：`不推荐使用 * 导入，导入变量不明确直观
    
    利用模块内置变量 `__all__` 进行 * 导入控制，定义 * 可以导入的全部变量
    
    c7.py    
    ```shell
        __all__ = ['a','c']
   
        a = 2
        c = 3
        d = 4
    ```
    c8.py
    ```shell
        from t.c7 import *
        
        print(a)
        print(c)
        print(d)
    ```

### 5.4 `__init__.py` 的用法

1. from import 换行
    * \n
    ```shell
        from c7 import a,b,\n
        c
    ```
    * ()
    ```shell
        from c7 import (a,b,
         c)
    ```
2. 作用一：当一个包被导入时，`__init__.py`文件被首先执行，可以使用内置变量`__all__`限制哪些模块被导入
3. 作用二：批量导入
   * 批量导入内置类库
   t包下的`__init__.py`文件
   ```shell
      import sys
      import datetime
      import io
   ```
   `c9.py`
   ```shell
      import t
      print(t.sys.path)
   ```
   `提示：`从当前包导入变量，form . import xx
   
### 5.5 包与模块的几个常见错误

1. 包和模块是不会被重复导入的
2. 避免循环导入 t1 -> t2 -> t3 -> t1

### 5.6 模块内置变量

1. 查看内置变量(当前模块所有变量)
    ```shell
       a = 1
       b = 2
       infos = dir()
       print(infos)
    ```
   输出：['__annotations__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'a', 'b']

   常用内置变量：
   * name：模块全路径
   * package：包全路径
   * doc：模块文档注释
   * file：当前模块完整物理路径
   ```shell
       print('__name__:'+__name__)
       print('__package__:'+__package__)
       # print('__doc__'+__doc__)
       print('__file__:'+__file__)
   ```

### 5.7 入口文件和普通模块内置变量的区别

1. 入口文件
    * 包名：NoneType
    * 模块名：`__main__`
    * file：当前模块相对路径，与模块所在目录有关
2. 普通模块
    * 包名：包全路径
    * 模块名：模块全路径
    * file：当前模块物理全路径

### 5.8 `__name__`的经典应用

1. dir 查看模块或类的变量
   
    ```shell
       import sys
       infos = dir(sys)
       print(infos)
    ```
2. make a script both importable and executable：判断一个 python 脚本是一个可以入口文件还是普通模块

    ```shell
        # 入口文件
        if __name__ == '__main__':
           print(This is app)
        
        print(This is a module)
    ```
3. 将入口文件当做模块运行,进入上层目录执行（模块所在包）

    ```shell
       # 无需加.py后缀
       python -m t.c15
    ```
   
### 5.9 相对导入和绝对导入

1. 顶级包

    入口文件同级的包为顶级宝，入口文件本身不属于任何包

2. 绝对导入：

   从顶级包开始到要导入模块的这个路径，入口文件可以使用绝对导入，无法使用相对导入（入口文件`__name__`为`__main__`），
   一定要使用相对导入，使用 python -m 进行运行

3. 相对导入：使用 . .. 代表当前目录，上层目录，从当前模块开始数，导入目标模块（本质根据`__name__`进行导）


## 6. 函数

### 6.1 认识函数

1. print(12),round(3.1415,2),help(print),help(round)

2. 特点

   * 功能性
   * 封装性，隐藏细节
   * 避免编写重复的代码

### 6.2 函数的定义及运行特点

1. 定义函数
    
   ```shell
        def funcname(parameter_list):
            pass
   ```
   * 参数列表可以没有
   * 返回结果使用 return value，没有 return 则返回 None
   * 函数需要先定义后调用
   * 定义一个函数：两个数字相加
   ```shell
        def add(x,y):
            result = x+y
            return result
   
        add(1,2)
   ```
   
2. 如何让函数返回多个值
    
    元组：
    ```shell
        def damage(skill1,skill2):
            
            damage1 = skill1 * 3
            damage2 = skill2 * 2
            return damage1,damage2
   
        # 调用1，返回元组tuple
        damages = damage(3,6)
        # 调用2，序列解包
        skill1_damage,skill2_damage = damage(4,8)
    ```
   
