---
title: maven3
tags: 
    - maven
index_img: https://s3.bmp.ovh/imgs/2023/02/02/a1786250a2cda9fa.png
# excerpt: 搭建自己的 maven 仓库
categories:
    - 后端
    - maven
---

## 1. 基础概念



maven 是一个项目构建工具。



## 2. 快速入门

### 2.1 配置 maven 环境变量

> 在 windows 系统下配置

* 下载 maven [传送门](https://maven.apache.org/download.cgi)
* 配置环境变量
  * MAVEN_HOME:  maven 的安装目录
  * Path: `;%MAVEN_HOME%\bin`
* 重启电脑
* 检测是否安装成功
  * mvn --version



### 2.2 maven 仓库介绍

* 仓库分类

  * 本地仓库
  * 远程仓库
    * 中央仓库
    * 私服
    * 其它公共仓库

* 本地仓库详解

  * 本地仓库：就是 maven 在本地存储的地方

  * maven 的本地仓库，在安装 maven 后并不会创建，它是在第一次执行 maven 命令的时候才被创建

  * maven 本地仓库的默认位置：无论是 windows 还是 linux，在用户的目录下都有一个.m2/repository/ 的仓库目录，这就是 maven 仓库的默认位置

    ```xml
    <settings>
    	<localRepository>目录</localRepository>
    </settings>
    ```

* 远程仓库-中央仓库详解

  * 最核心的中央仓库开始，maven 在安装的时候，自带的就是中央仓库的配置，可以通过修改 settings.xml 文件来修改默认的中央仓库地址

  * 中央仓库包含了绝大多数流行的开源 java 构件，以及源码、作者信息、SCM信息、许可证信息等。一般来说，简单的 java 项目依赖的构件都可以在这里下载到

    ```xml
    <repositores>
    	<repository>
        	<id>central</id>
            <name>Central Repository</name>
            <url>http://repo.maven.apache.org/maven2</url>
            <layout>default</layout>
            <snapshots>
            	<enabled>false</enabled>
            </snapshots>
        </repository>
    </repositores>
    ```

    

### 2.3 使用 IDEA 创建一个 maven 项目

  

1. 不使用 Archetype 创建（`提示：` IDEA 版本不同，功能项位置可能会变化（演示版本为：2022.1.4））

   ![image-20230202122801888](https://s3.bmp.ovh/imgs/2023/02/02/cc216f8f2baf0aa8.png)



2. 使用 Archetype 创建，使用 `maven-archetype-quickstart`

   ![image-20230202123100232](https://s3.bmp.ovh/imgs/2023/02/02/28b39021de8f3575.png)



3. maven 项目坐标

   > 在 maven 仓库中可定位到该项目（jar包/依赖）的唯一标记

   **GroupId**： 组织 ID，具有唯一性，常用网站域名反写，如 org.apache 、tech.fengjian

   **ArtifactId**：项目 ID，项目名称

   **Version**：版本号，默认为 1.0-SNAPSHOT

   

4. maven 项目标准目录结构

   * src

     * main
       * java -java源代码文件
       * resources -资源库
       * webapp
         * WEB-INF
           * index.jsp
         * css、js
       * Bin -脚本库
       * config -配置文件
       * filters -资源过滤库

   * test

     * java -java测试源代码文件
     * resources -测试资源库
     * filters -测试资源过滤库

   * target -存放项目构建后的文件和目录，比如 jar 包，war 包，编译的 class 文件

     

### 2.4 maven 核心 pom 文件



1. 什么是 pom

   pom 代表对象模型，它是 maven 中工作的基本组成单位。它是一个 XML 文件，始终保存在项目的基本目录中的 pom.xml 文件中。pom 包含的对象是使用 maven 来构建的，pom.xml 文件包含了项目的各种配置信息，需要特别注意，每个项目都只有一个 pom.xml 文件。

   

2. 项目配置信息

   * **project**：工程的根标签

   * **modelVersion**：pom 模型版本，maven2 和 maven3 只能为 4.0.0

   * **groupId**：这是工程组的标记。它在一个组织或者项目中通常是唯一的。例如一个银行组织 com.name.project 拥有所有和银行相关的项目

   * **artifactId**：这是工程的标识。它通常是工程的名称。例如，消费者银行。groupId 和 artifactId 一起定义了 artifact 在仓库中的位置

   * **versiion**：这是工程的版本号。在 artifact 的仓库中，它用来区分不同的版本

   * **packing**：定义 maven 项目的打包方式，有 jar 、war 和 ear 三种格式

     

3. 最小 pom 文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>tech.fengjian</groupId>
       <artifactId>maven-project</artifactId>
       <version>1.0-SNAPSHOT</version>
   </project>
   ```

   

4. super pom

   * 父（super）pom 是 maven 默认的 pom。所有的 pom 都继承自一个父 pom（无论是否显式定义了这个父 pom）。父 pom 包含了一些可以被继承的默认设置。因此，当 maven 发现需要下载 pom 中的依赖时，它会到 super pom 中配置的默认仓库。

   * 使用以下命令来查看 super pom 默认配置：

     ```she
     mvn help:effective-pom
     ```

   * 依赖配置信息（）
   
     1. dependencies
     
        ```xml
        <dependencies>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
            </dependency>
        </dependencies>
        ```
     
     					2. parent
     
             ```xml
             <parent>
                 <groupId>tech.fengjian</groupId>
                 <artifactId>maven-project-parent</artifactId>
                 <relativePath>/</relativePath>
                 <version>1.0</version>
             </parent>
             ```
     
             * groupId：父项目的组Id标识符
             * artifactId：父项目的唯一标识符
             * relativePath：Maven 首先在当前项目中找父项目的 pom，然后在文件系统的这个位置，然后再本地仓库，然后再远程仓库查找。
             * version：父项目的版本
     
     					3. modules
     
             * 有些 maven 项目会做成多模块的，这个标签用于指定当前项目包含的所有模块。之后对这个项目进行 maven 操作，会让所有的子模块进行相同操作。
     
               ```xml
               <modules>
               	<module>com-1</module>
                 <module>com-2</module>
                 <module>com-3</module>
               </modules>
               ```
     
     					4. properties
     
             * 用于定义 pom 常量
     
               ```xml
               <properties>
               	<java.version>1.8</java.version>
               </properties>
               ```
     
               上面这个常量可以在 pom 文件的任意位置，以 ${java.version} 的方式进行引用。
     
     					5. dependencyManagement
     
             * 应用场景
     
               * 当我们的项目模块很多的时候，我们依赖包的管理就会出现很多问题，为了项目的正确运行，必须让所有的子模块使用依赖项的同一版本，确保应用的各个项目的依赖项和版本一致，才能保证测试的和发布的是相同的结果。
     
             * 使用的好处
     
               * 在父模块中定义后，子模块不会直接使用对应依赖，但是在使用相同依赖的时候可以不加版本号，这样的好处是可以避免在使用该依赖的每个项目中声明版本号，这样想升级或切换到另一个版本号的时候，只需要在父类容器里更新，不需要任何一个子项目的更改。
     
               ```xml
               父项目：
               <dependencyManagement>
                   <dependencies>
                       <dependency>
                           <groupId>junit</groupId>
                           <artifactId>junit</artifactId>
                           <version>${java.version}</version>
                       </dependency>
                   </dependencies>
               </dependencyManagement>
               
               子项目1：
               <dependency>
                   <groupId>junit</groupId>
                   <artifactId>junit</artifactId>
               </dependency>
               
               子项目2：
               <dependency>
                   <groupId>junit</groupId>
                   <artifactId>junit</artifactId>
               </dependency>
               
               子项目2：
               <dependency>
                   <groupId>junit</groupId>
                   <artifactId>junit</artifactId>
                   <version>5.0</version>
               </dependency>
               ```
     
               
     
             



