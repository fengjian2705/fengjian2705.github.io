---
title: maven 知识点速查
tags: 
    - maven
index_img: https://s3.bmp.ovh/imgs/2023/02/02/a1786250a2cda9fa.png
# excerpt: 搭建自己的 maven 仓库
categories:
    - 后端
    - maven
---

## 1. 什么是 Maven?

Maven 是一个基于 Java的 项目管理工具，它可以帮助我们管理项目的依赖、构建、打包、发布等操作。与传统方式相比，使用 Maven 能够更加方便快捷地管理项目，尤其是当项目规模变得越来越大时，Maven的优势就更加明显了。

## 2. 安装与配置

###  2.1 在 windows 上安装 maven

1. 下载 maven [传送门](https://maven.apache.org/download.cgi)
2. 解压
3. 配置环境变量

* MAVEN_HOME:  maven 的安装目录 `D:\apache-maven-3.2.5`
* Path: `%MAVEN_HOME%\bin`

* 检测是否安装成功 `mvn --version`

### 2.2 maven 仓库分类

1. 本地仓库

   * 本地仓库：就是 maven 在本地存储的地方
   * maven 的本地仓库，在安装 maven 后并不会创建，它是在第一次执行 maven 命令的时候才被创建
   * maven 本地仓库的默认位置：无论是 windows 还是 linux，在用户的目录下都有一个.m2/repository/ 的仓库目录，这就是 maven 仓库的默认位置

   ```xml
   <settings>
   	<localRepository>目录</localRepository>
   </settings>
   ```

2. 远程仓库

   * 中央仓库

   * 私有仓库，个人或公司搭建

   * 其它公共仓库,如阿里云

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

1. 不使用骨架（Archetype） 创建（`tips：` IDEA 版本不同，功能项位置可能会变化（演示版本为：2022.1.4））

   ![](https://s3.bmp.ovh/imgs/2023/02/02/cc216f8f2baf0aa8.png)



2. 使用骨架（Archetype）创建，选择骨架 `maven-archetype-quickstart`

   ![image-20230202123100232](https://s3.bmp.ovh/imgs/2023/02/02/28b39021de8f3575.png)



3. maven 项目坐标

   **groupId**： 

   组 ID，定义当前 maven 项目隶属的实际项目。通常一个实际的项目和 Maven 项目不一定是一对一的关系，比如 springframework 项目就包含了诸多模块：spring-context、spring-core 等，那 spring-context、spring-core 就是 Maven 项目，而它们都被划分为一个项目组 springframework。

   另外为了保持组织 ID 具有唯一性，常用网站域名反写，如 org.springframework，那 spring-context、spring-core 的 groupId 就是 org.springframework

   **artifactId**：

   定义实际项目中的一个 Maven 项目（模块)，如 spring-core、spring-context

   **version**：

   版本号，默认为 1.0-SNAPSHOT

   **packaging**：

   ​	打包方式，默认 jar，还可以是 war、pom

   

4. maven 项目标准目录结构（约定优于配置）

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

   * **version**：这是工程的版本号。在 artifact 的仓库中，它用来区分不同的版本

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
     
        应用场景
     
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

### 2.5 maven 生命周期

1. 什么是生命周期

   maven 的生命周期就是对所有的构建过程进行抽象和统一。包含了项目的清理、初始化、编译、测试、打包、集成测试、验证、部署和站点生成等几乎所有的构件步骤。

2. maven 的三个构件生命周期

   * clean
     * pre-clean：执行一些清理前需要完成的工作
     * clean：清理上一次构件生成的文件
     * post-clean：执行一些清理后需要完成的工作
   * default
     * validate：验证工程是否正确
     * compile：编译项目源代码
     * test：使用合适的单元测试框架来测试已编译的源代码
     * package：把编译好的代码打包成一个可一个发布的格式（jar war)
     * verify：运行所有检查，验证包是否有效
     * install：安装到 maven 本地仓库
     * deploy：部署到远程的仓库，使得其他开发者或工程可以共享
   * site：生成项目信息、站点

### 2.6 常用的 maven 命令

* 常用命令

  ```shell
  mvn validate 验证项目是否正确
  mvn package maven 打包
  mvn generate-sources 生成源代码
  mvn compile 编译
  mvn test-compile 编译测试代码
  mvn test 运行测试
  mvn verify 运行检查
  mvn clean 清理项目
  mvn install 安装项目到本地仓库
  mvn deploy 发布项目到远程仓库
  mvn dependency:tree 显示 maven 依赖树
  mvn dependency:list 显示 maven 依赖列表
  ```

* 常用参数

  * -D：指定参数，如 -Dmaven.test.skip=true 跳过单元测试
  * -P：指定 Profile 位置，可用于区分环境

* web 相关命令

  * mvn tomcat:run 启动 tomcat
  * mvn jetty:run 启动 jetty
  * mvn tomcat:deploy 运行打包部署

## 3. jar 包管理

### 3.1 如何添加项目需要的 jar 包

1. 原理

   * 在本地，指定一个文件夹，便是 maven 的仓库，maven 会从远程的中央仓库中下载你所需要的 jar 资源到你本地，然后通过 maven 关联，将 jar 包依赖到你的项目中，避免了你需要将 jar 包添加到 lib 目录下，并通过 classpath 引入这些 jar 包的工作。

2. 步骤

   * 打开仓库网站（[传送门](https://mvnrepository.com/)）
   * 选择你需要的 jar 包的信息和版本
   * 填写依赖信息到 pom 文件
   * 下载到本地仓库
   * 项目使用

3. 实操

   ```xml
   <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
   <dependency>
       <groupId>org.apache.commons</groupId>
       <artifactId>commons-lang3</artifactId>
       <version>3.12.0</version>
   </dependency>
   
   ```

   

### 3.2 如何使用 maven 运行一个单元测试

1. 导入依赖

   ```xml
   <dependency>
       <groupId>junit</groupId>
       <artifactId>junit</artifactId>
       <scope>test</scope>
       <version>4.12</version>
   </dependency>
   ```

2. 编写测试代码

   ```java
   public class AppTest {
   
   
       @Test
       public void test(){
           System.out.println("junit test");
       }
   }
   ```

### 3.3 如何建立一个 web 应用

1. 从 Archetype 新建 web 项目

   ![image-20230207190017174](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207190017174.png)

2. 项目目录结构

   ![image-20230207190217734](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207190217734.png)

3. 新建 java 文件和 resources 文件

   ![image-20230207190814764](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207190814764.png)

   

### 3.4 如何导入第三方 jar 包到本地仓库

1. 搜索并下载 jar

   ![image-20230207191025207](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207191025207.png)

2. 导入

   * 进入 cmd 命令页面

   * 输入指令如下：

     ```shell
     mvn install:install-file -Dfile=xxxx -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.0 -Dpackaging=jar -DgeneratePom=true -DcreateChecksum=true
     ```

   * 参数说明：

     * -Dfile 为 jar 包文件路径
     * -DgroupId 一般为 jar 开发组织的名称，也是坐标 groupId
     * -DartifactId 一般为 jar 名称，也是坐标 artifactId
     * -Dversion 是版本号
     * -Dpackaging 是打包类型

### 3.5 常用的 maven 插件

1. 官方插件列表
   * groupId 为 org.apache.maven.plugins
   * http://maven.apache.org/plugins/index.html
2. 两种方式调用 maven 插件
   * 将插件目标与生命周期阶段绑定，例如 maven 默认将 maven-compiler-plugin 的 compile 与 maven 生命周期的 compile 阶段绑定
   * 直接在命令行显示指定要执行的插件目标，例如 mvn archetype:generate 就表示调用 maven-archetype-plugin 的 generate 目标
3. 常用的 maven 插件
   * maven-antrun-plugin
     * maven-antrun-plugin 能让用户在 maven 项目中运行 Ant 任务。用户可以直接在该插件的配置以 Ant 的方式编写 Target，然后交给该插件的 run 目标去执行
   * maven-archetype-plugin
     * archetype 指项目的骨架，maven 初学者最开始执行的 maven 命令可能就是 mvn archetype:generate，这实际上就是让 maven-archetype-plugin 生成一个很简单的项目骨架，帮助开发者快速上手
