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
    >>> print('Hello,World')
    >>> Hello,World
```
`提示：`
* python通常使用缩进来控制代码格式
* ;{}可以不用使用

## 2. Python 基本类型

### 2.1 Number 数字

1. 分类 

    整数：int
    浮点数：float

    其它语言：单精度（float），双精度（double）
    其它语言：short，int，long

2. 代码示例

    ```shell
        >>> type(1)
        >>> <class 'int'>
        >>> type(-1)
        >>> <class 'int'>
      
        >>> type(1.1)
        >>> <class 'float'>
        >>> type(1.1111111)
        >>> <class 'float'>
   
        type(1+1.2)
        <class 'float'>
        type(1+1.0)
        <class 'float'>
        type(1+1)
        <class 'int'>
    ```
