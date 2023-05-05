---
title: maven 使用手册
tags: 
    - maven
index_img: https://s3.bmp.ovh/imgs/2023/04/27/3648b97f5b46b3c8.png
excerpt: Maven 主要服务于基于 Java 平台的项目构建、依赖管理和项目信息管理
categories:
    - 后端
    - maven
---

> 阅读提示：本文内容假设你已经在实际项目中应用 Maven 进行项目开发，只为做进一步的理解与回顾。

## 1. 什么是 Maven

Maven 是一个基于 Java 的项目管理工具，它可以帮助我们管理项目的依赖、构建、打包、发布等操作。与传统方式相比，使用 Maven 能够更加方便快捷地管理项目，尤其是当项目规模变得越来越大时，Maven的优势就更加明显了。

## 2. 安装与配置

###  2.1 在 windows 上安装 maven

1. 安装 jdk
2. 下载 maven [传送门](https://maven.apache.org/download.cgi)
3. 解压
4. 配置环境变量

* MAVEN_HOME:  maven 的安装目录 `D:\apache-maven-3.2.5`
* Path: `%MAVEN_HOME%\bin`
* 检测是否安装成功 `mvn --version`

## 3. 坐标和依赖

### 3.1 maven 坐标

Maven 定义了这样一组规则:世界上任何一个依赖都可以使用 Maven 坐标唯一标识，Maven 坐标的元素包括 groupId、artifactId、version、packaging、classifier。现在，只要我们提供正确的坐标元素，Maven 就能在仓库中找到对应的依赖。

**groupId**： 

组 ID，定义当前 maven 项目隶属的实际项目。通常一个实际的项目和 Maven 项目不一定是一对一的关系，比如 springframework 项目就包含了诸多模块：spring-context、spring-core 等，那 spring-context、spring-core 就是 Maven 项目，而它们都被划分为一个项目组 springframework。

另外为了保持组织 ID 具有唯一性，常用网站域名反写，如 org.springframework，那 spring-context、spring-core 的 groupId 就是 org.springframework

**artifactId**：定义实际项目中的一个 Maven 项目（模块)，如 spring-core、spring-context

**version**：版本号，默认为 1.0-SNAPSHOT

**packaging**：打包方式，默认 jar，还可以是 war、pom

**classifier**: 该元素用来帮助定义构建输出的一些附属依赖，实际应用中很少涉及，不深入

上述 5 个元素中，groupld、artifactId，version 是必须定义的，packaging 是可选的(默认为jar)，而classifier是不能直接定义的。

### 3.2 依赖配置

在 pom.xml 中声明项目需要引入的依赖：

* 基本使用：包含基本坐标 groupId、artifactId，version 即可

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

* 更详细的使用：

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <type>xxx</type>
    <scope>xxx</scope>
    <optional>xxx</optional>
    <exclusions>
        <exclusion>
            <groupId>xxx</groupId>
            <artifactId>xxx</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

**type**： 对应引入依赖的 packging，无需声明，默认为 jar

**scope**：依赖作用范围，有 compile、test、provided、runtime、system、import，等下细说

**optional**：标记依赖是否可选

**exclusions**：用来排除传递性依赖

### 3.3 依赖范围

* Java 项目的 classpath 是指 Java 虚拟机（JVM）在加载类文件时所需要依赖的 class 文件路径的集合。可以将其理解为 Java 虚拟机查找 class 文件的路径列表。当 JVM 在加载 Java 程序时，它会先查找 classpath 中包含的路径，如果能够找到对应的 class 文件，就会将其加载进来，否则就会抛出 ClassNotFoundException 异常

* 在 Java 项目中，classpath 通常被设置为当前项目编译后生成的 class 文件所在的路径

* 首先需要知道,Maven 在编译项目主代码（src/main/java）的时候需要使用一套 classspath。其次,Maven 在编译和执行测试（src/main/test）的时候会使用另外一套 classpath。最后,实际运行Maven 项目的时候,又会使用一套 classpath,依赖范围就是用来控制依赖与这三种 classpath(编译 classpath、测试 classpath、运行 classpath)的关系
* compile：编译依赖范围。默认依赖范围。使用此依赖范围的 Maven 依赖,对于编译、测试、运行三种 classpath 都有效，典型的例子是 spring-core,在编译、测试和运行的时候都需要使用该依赖
* test：测试依赖范围。使用此依赖范围的Maven依赖,只对于测试 classpath 有效,在编译主代码或者运行项目的使用时将无法使用此类依赖。典型的例子,是 JUnit,它只有在编译测试代码及运行测试的时候才需要
* provided：已提供依赖范围。使用此依赖范围的 Maven 依赖,对于编译和测试 classpath 有效,但在运行时无效。典型的例子是 servlet-api,编译和测试项目的时候需要该依赖,但在运行项目的时候,由于容器已经提供,就不需要 Maven 重复地引入一遍
* runtime：运行时依赖范围。使用此依赖范围的 Maven 依赖,对于测试和运行 classpath 有效,但在编译主代码时无效。典型的例子是 JDBC 驱动实现项目主代码的编译只需要 JDK 提供的 JDBC 接口,只有在执行测试或者运行项目的时候才需要实现上述接口的具体 JDBC 驱动
* system：系统依赖范围。使用不多
* import：导人依赖范围。该依赖范围不会对三种 classpath 产生实际的影响，在项目继承中扮演角色，在讲解 maven 项目之间的继承时可以关注一下



用一张表格来归纳一下依赖的作用域：

| 依赖范围（scope） | 编译期有效 | 测试期有效 | 运行期有效 |               范例               |
| :---------------: | :--------: | :--------: | :--------: | :------------------------------: |
|      compile      |     Y      |     Y      |     Y      |           spring-core            |
|       test        |     -      |     Y      |     -      |              junit               |
|     provided      |     Y      |     Y      |     -      |           servlet-api            |
|      runtime      |     -      |     Y      |     Y      |          jdbc 驱动实现           |
|      system       |     Y      |     Y      |     -      | 本地的，maven 仓库之外的类库文件 |

### 3.3 依赖的传递性

![](https://s3.bmp.ovh/imgs/2023/05/04/dda9ba1603bbf614.png)

依赖传递性同样遵循依赖范围的影响：

|    -     | compile  | test | provided | runtime  |
| :------: | :------: | :--: | :------: | :------: |
| compile  | compile  |  -   |    -     | runtime  |
|   test   |   test   |  -   |    -     |   test   |
| provided | provided |  -   | provided | provided |
| runtime  | runtime  |  -   |    -     | runtime  |

**规律**：

当第二直接依赖的范围是 compile 的时候,传递性依赖的范围与第一直接依赖的范围一致;当第二直接依赖的范围是 test 的时候,依赖不会得以传递;当第二直接依赖的范围是 provided 的时候,只传递第一直接依赖范围也为 provided 的依赖,且传递性依赖的范围同样为 provided;当第二直接依赖的范围是 runtime 的时候,传递性依赖的范围与第一直接依赖的范围一致,但 compile 例外,此时传递性依赖的范围为runtime。

**举个栗子**：

第一列是第一直接依赖的作用域（scope），第一行是第二直接依赖的作用域，举个栗子：

现在有 A 直接依赖B（作用域test）：第一列取 test（3 行），B 直接依赖 C（作用域 compile）：第一行取 compile（2 列），得出 A 间接依赖 C（作用域 test）

### 3.4 传递依赖：依赖调解

依赖调解（Dependency Resolution）是指在 Maven 构建项目时，当多个依赖项存在冲突时，根据一定的规则解决冲突问题的过程。Maven 使用依赖调解机制来自动管理项目依赖，保证依赖项之间的协同工作。它会分析项目中的所有依赖项，并且通过比较各个依赖项之间的版本信息，确定应该使用哪一个版本作为最终的决策。

Maven 在进行依赖调解时，首先会检查项目中所有依赖项的直接依赖关系，然后逐级向上查找依赖项的依赖关系，直到找到最顶层的依赖项。如果在这个过程中发现了冲突，那么 Maven会 根据一定的决策原则选择其中一个版本作为最终的决策。具体来说，Maven 采用如下的决策原则：

1. 第一原则：路径最近者优先。举栗子：路径 1： A -> B -> C -> X(2.0)，路径2：A -> D -> X(1.0),根据最短路径优先，X(1.0)会被项目采用
2. 第二原则：第一声明者优先。举栗子：路径 1： A -> B -> C -> X(1.0)，路径 2：A -> D -> E -> X(2.0)，路径相同，则第一声明的 X(1.0) 会被采用

通过依赖调解机制，Maven 能够自动解决依赖项之间的冲突问题，提高了项目构建的效率，同时也降低了开发人员的工作难度。

### 3.5 可选依赖：optional

假设有这样一个依赖关系,项目 A 依赖于项目 B,项目 B 依赖于项目 X 和 Y,B 对于 X 和 Y 的依赖都是可选依赖：A->B、B->X(可选)、B->Y(可选)。根据传递性依赖的定义，如果所有这三个依赖的范围都是compile,那么X、Y就是是 A 的 compile 范围传递性依赖。然而,由于这里 X、Y 是可选依赖,依赖将不会得以传递。换句话说,X、Y 将不会对 A 有任何影响,如图所示。

![](https://s3.bmp.ovh/imgs/2023/05/04/2ccb17cfa95cfa7a.png)

### 3.6 传递依赖：依赖排除

传递性依赖会给项目隐式地引人很多依赖,这极大地简化了项目依赖的管理,但是有些时候这种特性也会带来问题。例如,当前项目有一个第三方依赖,而这个第三方依赖由于某些原因依赖了另外一个类库的 SNAPSHOT版本,那么这个 SNAPSHOT 就会成为当前项目的传递性依赖,而 SNAPSHOT 的不稳定性会直接影响到当前的项目。这时就需要排除掉该 SNAPSHOT,并且在当前项目中声明该类库的某个正式发布的版本。还有一些情况,你可能也想要替换某个传递性依赖。

使用 exclusions 标签：

```xml
<dependencies>
    <dependency>
        <groupId>org.example</groupId>
        <artifactId>D</artifactId>
        <version>1.0-SNAPSHOT</version>
        <exclusions>
            <exclusion>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```

### 3.7 归类依赖：统一版本管理

当引入了同一个项目的不同模块时，可以将 version 通过 properties 标签进行统一管理，方便后续的升级与代码简洁

```xml
<properties>
    <spring.version>5.2.12.RELEASE</spring.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>${spring.version}</version>
    </dependency>
</dependencies>
```

### 3.8 一个好用的 maven 插件

![](https://s3.bmp.ovh/imgs/2023/04/27/834d26b1a2d0e76f.png)



![](https://s3.bmp.ovh/imgs/2023/04/27/acdb79d9186a545c.png)

![](https://s3.bmp.ovh/imgs/2023/04/27/0bc04f7a069764fb.png)



## 4. Maven 仓库

得益于坐标机制,任何Maven项目使用任何一个构件的方式都是完全相同的。在此基础上,Maven可以在某个位置统一存储所有Maven项目去享的构件,这个统一的位置就是仓库。

### 4.1 依赖的布局

在 `pom.xml` 文件中的 `<repository>` 标签中，`layout` 元素用于指定 Maven 仓库的布局类型。Maven 仓库支持两种布局类型：默认布局和 Legacy 布局。

- 默认布局：Maven 2.0.9 及之后版本默认使用的布局类型，使用基于路径的布局方式。

  一个 maven 项目在仓库中的路径与坐标的大致对应关系为 groupld/artifactId/version/artifactld-version.packaging，另外 classifier 和 extension若存在，则在后面拼接上 -classifier.extension。

- Legacy 布局：Maven 2.0.8 及之前版本使用的布局类型，使用基于文件名的布局方式。

在 Maven 3.0 及之后的版本中，默认布局成为了唯一支持的布局类型，Legacy 布局已经被废弃。

如果你的 Maven 仓库使用的是 Legacy 布局，你需要在 `<repository>` 标签中设置 `layout` 属性，如下所示：

```xml
<repositories>
    <repository>
        <id>my-repo</id>
        <url>https://my-repo.com/maven</url>
        <layout>legacy</layout> <!-- 设置仓库布局类型为 Legacy -->
    </repository>
</repositories>
```

### 4.2 仓库分类

![](https://s3.bmp.ovh/imgs/2023/04/27/0e298b19a77242ce.png)

1. 本地仓库

   一般来说,在 Maven 项目目录下,没有诸如 lib/ 这样用来存放你衣赖文件的目录。当 Maven 在执行编译或测试时,如果需要使用依赖文件,它总是基于坐标使用本地仓库的依赖文件

   * 本地仓库：就是 maven 在本地存储的位置
   * maven 的本地仓库，在安装 maven 后并不会创建，它是在第一次执行 maven 命令的时候才被创建
   * maven 本地仓库的默认位置：无论是 windows 还是 linux，在用户的目录下都有一个 .m2/repository/ 的仓库目录，这就是 maven 仓库的默认位置

   ```xml
   <settings>
   	<localRepository>C:\Users\fengjian\.m2\repository</localRepository>
   </settings>
   ```

   * 有时候,因为某些原因(例如 C 盘空间不够),用户会想要自定义本地仓库目录地址。这时,可以编辑文件 ~/.m2/settings.xml,设置 localRepository 元素的值为想要的仓库地址。例如:

   ```xml
   <settings >
   	<localRepository >D:\java\repository\</localRepository
   </settings >
   ```

   * 需要注意的是,默认情况下, .m2/settings.xml 文件是不存在的,用户需要从 Maven 安装目录复制 %M2_HOME%/conf/settings.xml 文件再进行编辑。推荐大家不要直接修改全局目录的settings.xml 文件,推荐使用用户范围的 settings.xml,主要是为了避免无意识地影响到系统中的其他用户，还有就是升级时就不需要重新修改 settings.xml 文件
   * **mvn clean install**：一个项目（构建、依赖）只有在本地仓库中后，才能由其他 Maven 项目使用，install 命令就是将一个项目安装到本地仓库，比如：本地有两个项目 A 和 B，两者都无法从远程仓库获取，同时 A 依赖 B，那么为了 A 可以有效的构建，B 必须安装到本地仓库中

2. 远程仓库

   安装好 Maven 后,如果不执行任何 Maven 命令,本地仓库目录是不存在的。当用户输人第一条 Maven 命令之后,Maven 才会创建本地仓库,然后相据配置和需要,从远程仓库下载构件至本地仓库。

   本地仓库就好比书房,我需要读书的时候先从书房找,相应应地,Maven需要构件的时候先从本地仓库找。远程仓库就好比书店(包括实体书店、网上书店等),当我无法从自己的书房找到需要的书的时候,就会从书店购买后放到书房里。当Maven无法从本地仓库找到需要的构件的时候,就会从远程仓库下载构件至本地仓库。一般地,对于每个人来说,书房只有一个,但外面的书店有很多,类似地,对于Maven来说,每个用户只有一个本地仓库,但可以配置访问很多远程仓库。

   * 中央仓库（maven 官方提供的仓库）

     由于最原始的本地仓库是空的,中央仓库就是这样一个默认的远程仓库,Maven的安装文件自带了中央仓库的配置。解压 %M2_HOME%/lib/maven-mod-el-builder-3.0.jarorg文件 /apache/maven/model/pom-4.0.0. xml,可以看到如下的配置:

     ```xml
     <repositories>
         <repository >
         	<id>central</id>
         	<name>Maven Repository Switchboard</name >
         	<url>http://repol.maven.org/maven2</url>
         	<layout>default</layout>
         	<snapshots >
         		<enabled > false </enabled >
         	</snapshots >
     	</repository>
     </repositories>
     
     ```

   * maven 在安装的时候，自带的就是中央仓库的配置，可以通过修改 settings.xml 文件来修改默认的中央仓库地址


   * 中央仓库包含了绝大多数流行的开源 java 构件，以及源码、作者信息、SCM 信息、许可证信息等。一般来说，简单的 java 项目依赖的构件都可以在这里下载到

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


* 私有仓库（一种特殊的远程仓库），个人或公司搭建

  私服是一种特殊的远程仓库,它是架设在局域网内的仓库服务,私服代理广域网上的远程仓库,供局域网内的Maven用户使用。当Maven需要下载构件的时候,它从私服请求,如果私服上不存在该构件,则从外部的远程仓库下载,缓存在私服上之后,再为Maven的下载请求提供服务。此外,一些无法从外部仓库下载到白的构件也能从本地上传到私服上供大家使用

* 其它公共仓库，如阿里云

### 4.3 远程仓库的配置

1. 在很多情况下,默认的中央仓库无法满足项目的需求,可能项目需要的构件存在于另外一个远程仓库中,如阿里云仓库。这时,可以在 POM 中配置该仓库：

```xml
<repositories>
    <repository>
        <id>aliyun</id>
        <name>aliyun</name>
        <url>https://maven.aliyun.com/repository/public</url>
        <layout>default</layout>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>daily</updatePolicy>
            <checksumPolicy>ignore</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>daily</updatePolicy>
            <checksumPolicy>ignore</checksumPolicy>
        </snapshots>
    </repository>
</repositories>
```

* 在 repositories 元素下,可以使用 repository 子元素声明一个或者多个远程仓库。该例中声明了一个id为 aliyun。任何一个仓库声明的id必须是唯一的,尤其需要注意的是,Maven 自带的中央仓库使用的id为central,如果其他的仓库声明也使用该 id,就会覆盖中央仓库的配置。该配置中的ur值指向了仓库的地址,一般来说,该地址都基于http协议,Maven用户都可以在浏览器中打开仓库地址浏览构件。

* 如果在 `pom.xml` 文件中的 `<repositories>` 标签中没有设置 `<snapshots>` 标签，那么 Maven 会使用默认值来处理快照依赖的查找和下载

  默认情况下，Maven 会从远程仓库中查找和下载快照依赖的最新版本。如果远程仓库中没有最新的快照版本，Maven 会从本地仓库中查找，并下载最新的快照版本到本地仓库中。在实际开发中，建议将快照依赖的使用限制在开发和测试阶段，避免在生产环境中使用快照依赖。

* 如果在 `<repository>` 标签中没有设置 `name` 属性，那么 Maven 会将仓库的名称默认设置为 `<id>` 标签的值，即仓库的 `id` 属性值。

  需要注意的是，虽然 Maven 会将仓库的名称默认设置为 `<id>` 标签的值，但是在 `<repository>` 标签中，`name` 属性的作用是更加明确和具体的，可以为仓库指定一个更加具有描述性的名称，便于其他开发者理解和识别。因此，建议在 `<repository>` 标签中显式地设置 `name` 属性。

* `<releases>` 属性用于指定仓库是否支持发布版本，如果不设置该属性，Maven 会将其默认设置为 `true`。

2. 对于 `<releases>` 和 `<snapshots>` 来说,除了 enabled,它们还包含另外两个子元素 updatePolicy 和 checksumPolicy:

`<updatePolicy>`：用来配置Maven从远程仓库检查更新的频率,默认的值是daily,表示 Maven 每天检查一次。其他可用的值包括 never 从不检查更新，always 每次构建都检查更新，interval:X 每隔X分钟检查一次更新(X为任意整数))

`<checksumPolicy>`：用来配置 Maven 检查检验和文件的策略。当构件被部署到 Maven 仓库中时,会同时部署对应的校验和文件。在下载构件的时候,Maven会验证校验和文件,如果校验和验证失败,怎么办?当 checksumPolicy 的值为默认的 warn 时,Maven会在执行构建时输出警告信息,其他可用的值包括:fail Maven遇到校验和错误就让构建失败,ignore 使Maven完全忽略校验和错误。

### 4.4 远程仓库的认证配置

1. 大部分远程仓库无须认证就可以访问,但有时候出于安全方面的考虑,我们需要提供认证信息才能访问一些远程仓库。例如,组织内部有一个 Maven 仓库服务器,该服务器为每个项目都提供独立的 Maven 仓库,为了防止非法的仓库访问,管理员为每个仓库提供了一组用户名及密码。这时,为了能让 Maven 能访问仓库,就需要配置认证证信息

2. 配置认证信息和配置仓库信息不同,仓库信息可以直接配置在 POM 文件中,但是认证信息必须配置在 settings.xml 文件中。这是因为 POM 往往是被提交到代码仓库中供所有成员访问的,而 settings.xml 一般只放在本机。因此,在 settings.xml 中配置认证信息更为安全。假设需要为一个 id 为 fengjian-maven-central 的仓库配置认证信息,编辑 settings.xml:

```xml
<servers>
	<server>
      <id>fengjian-maven-central</id>
      <username>jack</username>
      <password>123456</password>
    </server>
</servers>	
```

### 4.5 部署项目到远程仓库

1. 这里的远程仓库是指自己搭建的私有仓库，搭建仓库可以去看另一篇博文`使用 Nexus 搭建 Maven 私有仓库`

2. `pom.xml`中配置部署信息：分为 release 和 snapshot

   ```xml
     <distributionManagement>
       <repository>
           <id>fengjian-maven-releases</id>
           <url>http://localhost:8081/repository/fengjian-maven-release/</url>
       </repository>
       <snapshotRepository>
           <id>fengjian-maven-snapshots</id>
           <url>http://localhost:8081/repository/fengjian-maven-snapshots/</url>
       </snapshotRepository>
   </distributionManagement>
   ```

3. `settings.xml`中配置认证信息

   ```xml
   <servers>
   	<server>
         <id>fengjian-maven-releases</id>
         <username>jack</username>
         <password>123456</password>
   	</server>
       <server>
         <id>fengjian-maven-snapshots</id>
         <username>rose</username>
         <password>654321</password>
   	</server>
   </servers>
   
   ```

4. 配置正确后,在命令行运行`mvn clean deploy`,Maven 就会将马目构建输出的构件部署到配置对应的远程仓库,如果项目当前的版本是快照版本,则部署到快照版本仓库地址,否则就部署到发布版本仓库地址。

### 4.6 镜像 mirror

1. 如果仓库 A 可以提供仓库 B 存储的所有内容,那么就可以认为 A 是 B 的一个镜像。换句话说,任何一个可以从仓库 B 获得的构件,都能够从它的镜像中获取。举个例子,https://maven.aliyun.com/repository/public/ 是中央仓库 https://repo.maven.apache.org/maven2 在中国的镜像,由于地理位置的因素,该镜像往往能够提供比中央央仓库更快的服务。因此,可以配置Maven使用该镜像来替代中央仓库，在 settings.xml 中配置：

```xml
<mirrors>
    <mirror>
      <id>aliyun</id>
      <name>aliyun</name>
      <url>https://maven.aliyun.com/repository/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
</mirrors>
```

该例中,`<mirrorOf>`的值为 central,表示该配置为中央仓库的镜像,任何对于中央仓库的请求都会转至该镜像,用户也可以使用同样的方法配置其他仓库的镜像。另外三个元素id、name、url与一般仓库配置无异,表示该镜像仓库的吻一标识符、名称以及地址。类似地,如果该镜像需要认证,也可以基于该 id 配置仓库认证。

2. 关于镜像的一个更为常见的用法是结合私服。由于私服可以代理任何外部的公共仓库(包括中央仓库),因此,对于组织内部的 Maven 用户来说,使用一个私服地址就等于使用了所有需要的外部仓库,这可以将配置集中到私服,从而简化 Maven 本身的配置。在这种情况下,任何需要的构件都可以从私服获得,私服就是所有仓库的镜像。这时,可以配置这样的一个镜像:

```xml
<mirrors>
    <mirror>
      <id>fengjian-maven-central</id>
      <name>fengjian-maven-central</name>
      <url>http://localhost:8081/repository/fengjian-maven-central/</url>
      <mirrorOf>*</mirrorOf>
    </mirror>
</mirrors>
```

为了满足一些复杂的需求,Maven还支持更高级的镜像配置量:

`<mirrorOf>*</mirrorOf>`: 匹配所有远程仓库。
`<mirrorOf>external:*</mirrorOf>`: 匹配所有远程仓库,使用 localhost 的除外,使用 file:// 协议的除外。也就是说,匹配所有不在本机上的远和程仓库
`<mirrorOf>repo1, repo2</mirrorOf>`: 匹配仓库repol和repo2,使用逗号分隔多个远程仓库。
`<mirrorOf>*,!repol</mirrorOf>`: 匹配所有远程仓库,repol除外,使用感叹号将仓库从匹配中排除。

3. 需要注意的是,由于镜像仓库完全屏蔽了被镜像仓库,当镜像仓库不稳定或者停止服务的时候,Maven 仍将无法访问被镜像仓库,因而将无法去下载依赖

### 4.7 一些常用的 maven 搜索库

1. 地址:http://repository.sonatype.org/ Nexus 是当前最流行的开源Maven仓库管理软件
2. 地址:http://www.jarvana.com/jarvana/ Jarvana 提供了基于关键字、类名的搜索,构件下载、依赖声明片段等功能也一应俱全
3. 地址:http://mvnrepository.com/ MVNrepository 的界面比较清新,它提供了基于关键字的搜索、依赖声明代码片段、构件下载、依赖与被依赖关系信息、构件所含包信息等功能

## 5. Maven 生命周期

Maven 的生命周期就是为了对所有的构建过程进行抽象和统一建。这个生命周期含了项目的清理、初始化、编译、测试、打包、集成测试、验证、部署和站点生成等几所有构建步骤。也就是说,几乎所有项目的构建,都能映射到这样一个生命周期上。

### 5.2 默认的生命周期

- `validate`- 验证项目是否正确，以及所有必要的信息是否可用
- `compile`- 编译项目的源代码
- `test`- 使用合适的单元测试框架测试编译的源代码。这些测试不应要求打包或部署代码
- `package`- 获取编译后的代码并将其打包为其可分发的格式，例如 JAR。
- `verify`- 对集成测试结果进行任何检查，以确保满足质量标准
- `install`- 将包安装到本地存储库中，以用作本地其他项目中的依赖项
- `deploy`- 在构建环境中完成，将最终包复制到远程存储库，以便与其他开发人员和项目共享。

### 5.1 三套相互独立的生命周期

Maven 的生命周期跟你理解的生命周期有一点不同，它有三套，具体可见[官方文档]([Maven – Introduction to the Build Lifecycle (apache.org)](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html))

1. clean：清理项目
2. default：构建项目
3. site：建立站点

这三个生命周期涵盖了下图（我们在 IDEA 项目中所看到的 Lifecycle）：

![](https://s3.bmp.ovh/imgs/2023/04/28/eba46fcc000d3d0b.png)

接下来我们单独介绍每一套生命周期：

#### 5.1.1 clean 生命周期

| 阶段         | 描述                             |
| :----------- | :------------------------------- |
| `pre-clean`  | 在实际项目清理之前执行所需的流程 |
| `clean`      | 删除先前版本生成的所有文件       |
| `post-clean` | 执行完成项目清理所需的流程       |

#### 5.1.2 default 生命周期

| 阶段                      | 描述                                                         |
| :------------------------ | :----------------------------------------------------------- |
| `validate`                | 验证项目是否正确，以及所有必要的信息是否可用。               |
| `initialize`              | 初始化构建状态，例如设置属性或创建目录。                     |
| `generate-sources`        | 生成任何源代码以包含在编译中。                               |
| `process-sources`         | 处理源代码，例如过滤任何值。                                 |
| `generate-resources`      | 生成要包含在包中的资源。                                     |
| `process-resources`       | 将资源复制并处理到目标目录中，准备打包。                     |
| `compile`                 | 编译项目的源代码。                                           |
| `process-classes`         | 对编译中生成的文件进行后处理，例如对 Java 类进行字节码增强。 |
| `generate-test-sources`   | 生成任何测试源代码以包含在编译中。                           |
| `process-test-sources`    | 处理测试源代码，例如筛选任何值。                             |
| `generate-test-resources` | 创建用于测试的资源。                                         |
| `process-test-resources`  | 将资源复制并处理到测试目标目录中。                           |
| `test-compile`            | 将测试源代码编译到测试目标目录                               |
| `process-test-classes`    | 对测试编译生成的文件进行后处理，例如对 Java 类进行字节码增强。 |
| `test`                    | 使用合适的单元测试框架运行测试。这些测试不应要求打包或部署代码。 |
| `prepare-package`         | 在实际打包之前执行准备包裹所需的任何操作。这通常会导致包的解包、已处理版本。 |
| `package`                 | 获取已编译的代码并将其打包为其可分发格式，例如 JAR。         |
| `pre-integration-test`    | 执行执行集成测试之前所需的操作。这可能涉及设置所需环境等事项。 |
| `integration-test`        | 如有必要，处理包并将其部署到可以运行集成测试的环境中。       |
| `post-integration-test`   | 执行集成测试后所需的操作。这可能包括清理环境。               |
| `verify`                  | 运行任何检查以验证包是否有效并符合质量标准。                 |
| `install`                 | 将包安装到本地存储库中，以便在本地其他项目中用作依赖项。     |
| `deploy`                  | 在集成或发布环境中完成，将最终包复制到远程存储库，以便与其他开发人员和项目共享。 |

#### 5.1.3 站点生命周期

| 阶段          | 描述                                     |
| :------------ | :--------------------------------------- |
| `pre-site`    | 在实际项目网站生成之前执行所需的流程     |
| `site`        | 生成项目的站点文档                       |
| `post-site`   | 执行完成站点生成和准备站点部署所需的流程 |
| `site-deploy` | 将生成的站点文档部署到指定的 Web 服务器  |





## 6. Maven 插件

"Maven" 实际上只是 Maven 插件集合的核心框架。换句话说，插件是执行大部分实际操作的地方，插件用于：创建jar文件，创建war文件，编译代码，单元测试代码，创建项目文档等等。您能想到的对项目执行的几乎所有操作都是作为Maven插件实现的。

插件是 Maven 的核心功能，它允许在多个项目中重用通用构建逻辑。他们通过在项目描述的上下文中执行“操作”（即创建 WAR 文件或编译单元测试）来做到这一点 - 项目对象模型 （POM）。插件行为可以通过一组独特的参数进行自定义，这些参数由每个插件目标（或Mojo）的描述公开。

插件是 Maven 的核心功能，它允许在多个项目中重用通用构建逻辑。他们通过在项目描述的上下文中执行“操作”（即创建 WAR 文件或编译单元测试）来做到这一点 - 项目对象模型 （POM）。插件行为可以通过一组独特的参数进行自定义，这些参数由每个插件目标（或Mojo）的描述公开。

### 6.1 插件配置

几乎所有Maven插件的目标都有一些可配置的参数,用户可以通过命令行和POM配置等方式来配置这些参数。

1. 命令行插件配置

   在日常的 Maven 使用中,我们会经常从命令行输入并执行Maven命令。很多插件目标的参数都支持从命令行配置,用户可以在Maven命令中使用-D参数,并伴随一个个参数键=参数值的形式,来配置插件目标的参数。例如,maven-surefire-plugin 提供了一个 maven.test.skip 参数,当其值为 true 的时候,就会跳过执行测试。于是,在运行命令的时候,加上如下 -D 参数就能跳过测试 mvn install -Dmaven.test.skip = true 参数-D是 Java 自带的,其功能是通过命令行设置一个Java系统属性,Maven 简单地重用了该参数,在准备插件的时候检查系统属性,便实现了插件参数的配置。

2. POM 中插件全局配置

   并不是所有的插件参数都适合从命令行配置,有些参数的值从项目创建到项目发布都不会改变,或者说很少改变,对于这种情况,在POM文件中一次性配置就显然比重复在命令行输入要方便。用户可以在声明插件的时候,对此插件进行一个全局的配置。也就是说,所有该基于该插件目标的任务,都会使用这些配置。例如,我们通常会需要配置 maven-compiler-plugin 告诉它编译 Java1.8 版本的源文件,生成与JVM1.8兼容的字节码文件：

   ```xml
   <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-compiler-plugin</artifactId>
       <version>3.8.1</version>
       <configuration>
           <source>1.8</source>
           <target>1.8</target>
       </configuration>
   </plugin>
   ```

### 6.2 获取插件信息（寻找插件）

以下是几个常用的 Maven 插件仓库地址：

- Apache Maven Central Repository：Maven 官方中央仓库。[[1](https://search.maven.org/)]
- JCenter：由 Bintray 提供的免费 Maven 仓库。[[2](https://bintray.com/bintray/jcenter)]
- Spring Plugins Repository：Spring 官方提供的 Maven 插件仓库。[[3](https://repo.spring.io/plugins-release/)]
- Google Maven Repository：由 Google 提供的 Maven 仓库，包含 Android 和 Google Cloud 相关的依赖。[[4](https://maven.google.com/)]

## 7. 关于聚合和继承这件事

软件设计人员往往会采用各种方式对软件划分模块,以得到更清晰的设计及更高的重用性。当把 Maven 应用到实际项目中的时候,也需要将项目分成不同的模块。Maven 的聚合特性能够把项目的各个模块聚合在一起构建,而 Maven 的继承特性则能帮助抽取各模块相同的依赖和插件等配置,在简简化 POM 的同时,还能促进各个模块配置的一致性。

以一个购物网站项目为例：我们把该项目拆分为'下单模块'和'发货模块'，分别为 shopping-order 和 shopping-delivery：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>tech.fengjian</groupId>
    <artifactId>shopping-order</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>Shopping Order</name>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>tech.fengjian</groupId>
    <artifactId>shopping-delivery</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>Shopping Delievery</name>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

一般来说,一个项目的子模块都应该使用同样的 groupld,如果它们一起开发和发布,还应该使用同样的 version,此外,它们的 artifactld 还应该使用一致的前缀,以方便同其他项目区分。

### 7.1 聚合

上面我们创建了两个项目，那一个简单的需求就会自然而然地显现出来:我们会想要一次构建两个项目,而不是到两个模块的日录下分别执行 mvn 命令。Maven 聚合(或者称为多模块)这一特性就是为该需求服务的。
为了能够使用一条命令就能构建 shopping-order 和 shopping-delivery 两个模块,我们需要创建一个额外的名为 shopping-aggregator 的模块,然后通过该模块构建整个项目的所有模块。shopping-aggregator 本身作为一个 Maven 项目,它必须要有自己的 POM,不过,同时作为一个聚合项目,其 POM 又有特殊的地方。如下为 shopping-aggregatorf的pom.xml内容:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>tech.fengjian</groupId>
    <artifactId>shopping-aggregator</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>Shopping Aggregator</name>

    <modules>
        <module>../shopping-delivery</module>
        <module>../shopping-order</module>
    </modules>

</project>
```

* 上述 POM 依旧使用了购物网站服务共同的 tech.fengjian, artifactld 为独立的 shopping-aggregator,版本也与其他两个模块-致,为 1.0-SNAPSHOT。这里的第一个特殊的地方为 packaging,其值为 POM。回顾 shopping-order 和 shopping-delivery,它们都没有声明 packaging,即使用了默认值 jar。对于聚合模块来说,其打包方式 packaging 的值必须为 pom,否则就无法构建。

* modules 是实现聚合的最核心的配置，我们可以通过在一个打包方式为 pom 的 Maven 项目中声明任意数量的 module 元素来实现模块聚合。这里的每一个 module 值都是一个当前 POM 的相对目录。

  一般来说,为了方便快速定位内容,模块所处的目录名称应当与其 artifactld 一致,不过这不是Maven的要求,用户也可以将shopping-order 项目放到 order-shopping/ 目录下。这时聚合的配置就需要相应地改成<module>order-shopping</module>。

* 为了方便用户构建项目、通常将聚合模块放在项目目录的最顶层,其他模块则作为聚合模块的子目录存在,这样当用户得到源码的时候,第一眼发现的就是聚合模块的 POM,不用从多个模块中去寻找聚合模块来构建整个项目。

* 关于目录结构还需要注意的是,聚合模块与其他模块的目录结构并非一定要是父子关系，还可以是另一种平行的目录结构。

  ![平行目录结构聚合工程](https://s3.bmp.ovh/imgs/2023/05/05/58f1ec49485695d5.png)

  ![父子目录结构绝活](https://s3.bmp.ovh/imgs/2023/05/05/151d74c13eec1a0f.png)

   

* 统一构建，在聚合工程上进行操作即可（shopping-aggregator），注意这里显示的是 pom 中的 <name> 标签中的值，如果没有定义 name 则默认显示的是 artifactId

  ![](https://s3.bmp.ovh/imgs/2023/05/05/0b8509167c3379c8.png)

### 7.2 继承

到目前为止,我们已经能够使用 Maven 的聚合特性通过一条命令同时构建 shopping-order 和 shopping-delivery 两个模块,不过这仅仅解决了多模块 Maven 项目的一个问题。那么多模块的项目还有什么问题呢?这两个 POM 有着很多相同的配置,例如它们有相同的 groupId 和 version,有相同的 junit 依赖,还有相同的 maven-compiler-plugin 配置。大量的前人经验告诉我们,重复往往就意味着更多的劳动和更多的潜在的问题。在面向对象世界中,程序员员可以使用类继承在一定程度上消除重复,在 Maven 的世界中,也有类似的机制能让我们抽取出重复的配置,这就是 POM 的继承。

* 新建 shopping-parent 模块

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>tech.fengjian</groupId>
      <artifactId>shopping-parent</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>pom</packaging>
      <name>Shopping Parent</name>
  
  </project>
  ```

* 修改子模块 shopping-order、shopping-delivery 继承 shopping-parent

  * shopping-order

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <parent>
            <artifactId>shopping-parent</artifactId>
            <groupId>tech.fengjian</groupId>
            <version>1.0-SNAPSHOT</version>
            <relativePath>../shopping-parent/pom.xml</relativePath>
        </parent>
    
        <artifactId>shopping-order</artifactId>
        <name>Shopping Order</name>
    
        <dependencies>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

  * shopping-delivery

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <parent>
            <artifactId>shopping-parent</artifactId>
            <groupId>tech.fengjian</groupId>
            <version>1.0-SNAPSHOT</version>
            <relativePath>../shopping-parent/pom.xml</relativePath>
        </parent>
    
        <artifactId>shopping-delivery</artifactId>
        <name>Shopping Delievery</name>
    
        <dependencies>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.12</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    
    </project>
    ```

上述 POM 中使用 parent 元素声明父模块,parent 下的子元素 groupId、artifactId 和 version 指定了父模块的坐标,这三个元素是必须的。元素 relativePath 表示父模块 POM 的相对路径,该例中的../shopping-account/pom.xml 表示父 POM 的位置在与 shopping-order/ 目录平行的 shopping-account/ 目录下。当项目构建时,Maven 会首先根据 rellativePath 检查父 POM,如果找不到,再从本地仓库查找。relativePath 的默认值是../pom.xml,也就是说,Maven 默认父 POM 在上一层目录下。正确设置 relativePath 非常重要。考虑这样一个情况,开发团队的新成员从源码库签出一个包含父子模块关系的 Maven 项目。由于只关心其中的某一个子模块,它就直接到该模块的目录下执行构建,这个时候,父模块是没有被安装到本地仓库的,因此如果子模块没有设置正确的 relativePath,Maven 将无法找到父 POM,这将直接导致构建失败。如果 Maven 能够根据 relativePath 找到父POM,它就不需要再去检查本地仓库。这个更新过的 POM 没有为 shopping-order 声明 groupId 和 version,不过这并不代表 shopping-order 没有 groupld 和 version。实际上,这个子模块隐式地从父模均快继承了这两个元素,这也就消除了一些不必要的配置。在该例中,父子模块使用同样的 groupId 和 version,如果遇到子模块需要使用和父模块不一样的 groupld 或者 version 的情况,那么用户完全可以在子模块中显式声明。对于 artifactld 元素来说,子模块应该显式声明,一方面,如果完全继承 groupId、artifactld 和 version,会造成坐标冲突;另一方面,即使使用不同的 groupId 或 version,同样的 artifactld 容易造成混淆。

* 除了 groupId 和 version 可以被继承，还有一些元素也同样可以被继承，下表是完整的可继承的列表

  | 元素                   | 说明                                                         |
  | ---------------------- | ------------------------------------------------------------ |
  | groupld                | 项目组ID,项目坐标的核心元素                                  |
  | version                | 项目版本,项目坐标的核心元素                                  |
  | description            | 项目的组织信息                                               |
  | organization           | 项目的组织信息                                               |
  | inceptionYear          | 项目的创始年份                                               |
  | url                    | 项目的URL地址                                                |
  | developers             | 项目的开发者信息                                             |
  | contributors           | 项目的贡献者信息                                             |
  | distributionManagement | 项目的部署配置                                               |
  | issueManagement        | 项目的缺陷跟踪系统信息                                       |
  | ciManagement           | 项目的持续集成系统信息                                       |
  | scm                    | 项目的版本控制系统信息                                       |
  | mailingLists           | 项目的邮件列表信息                                           |
  | properties             | 自定义的Maven属性                                            |
  | dependencies           | 项目的依赖配置                                               |
  | dependencyManagement   | 项目的依赖管理配置                                           |
  | repositories           | 项目的仓库配置                                               |
  | build                  | 包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等 |
  | reporting              | 包括项目的报告输出目录配置、报告插件配置等                   |

* 依赖管理

  模块 shopping-order 和模块 shopping-delivery 都依赖 junit，那很自然的想到把 junit 依赖从子模块中抽取到 shopping-parent 中。这样就会产生一个问题，如果现在又新增了一个子母块 shopping-a，而 shopping-a 并不需要 junit 依赖，上面直接抽取的做法会强加给 a 一个 junit 依赖。

  Maven 提供了依赖管理标签来解决这种问题，Maven 提供的 dependencyManagement 元素既能让子模块的依赖配置,又能保证子模块依赖使用的灵活性。在dependencyManagement元素下的依赖声明不会引人实际的依赖,不过它能够约束 dependencies 下的依赖使用。例如,可以在 shopping-parent 中加入这样的 dependencyManagement 配置:

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>tech.fengjian</groupId>
      <artifactId>shopping-parent</artifactId>
      <version>1.0-SNAPSHOT</version>
      <packaging>pom</packaging>
      <name>Shopping Parent</name>
  
      <modules>
          <module>../shopping-delivery</module>
          <module>../shopping-order</module>
      </modules>
  
      <properties>
          <junit.version>4.12</junit.version>
      </properties>
  
      <dependencyManagement>
          <dependencies>
              <dependency>
                  <groupId>junit</groupId>
                  <artifactId>junit</artifactId>
                  <version>${java.version}</version>
                  <scope>test</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
  </project>
  ```

  * 首先该父 POM 将 junit 依赖的版本以 Maven 变量的形式提取了出来,不仅消除了一些重复,也使得各依赖喷的版本处于更加明显的位置。

  * 这里使用 dependencyManagement 声明的依赖既不会给 shopping-parent 引人依赖,也不会给它的子模块引入依赖,不过这段配置是会被继承的。

    shopping-order 和 shopping-delivery 如果需要使用 junit，则可配置：

    ```xml
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
    ```

    父 POM中 使用 dependencyManagement 声明依赖能够统一项目范围中依赖的版本,当依赖版本在父 POM中 声明之后,子模块在使用你赖的时候就无须声明版本,也就不会发生多个子模块使用依赖版本不一致的情况:这可以帮助降低依赖冲突的几率。

    如果子模块不声明依赖的使用,即使该依赖已经在父 POM的dependeneyManagement 中声明了,也不会产生任何实际的效果。

* 在介绍依赖范围的时候提到了名为 import 的依赖范围,推迟到现在介绍是因为该范围的依赖只在 dependencyManagement 元素下才有效果,使用该范围国的依赖通常指向一个 POM,作用是将目标 POM 中的 dependencyManagement 配置导人并合并到当前 POM 的 dependencyManagement 元素中。例如想要在另外一个模块中使用与 shopping-parent 完全一样的 dependencyManagement 配置,除了复制配置或者继承这两种方式之外,还可以使用 import 范围依赖将这一配置导入：

  ```xml
   <dependencyManagement>
      <dependencies>
          <dependency>
              <groupId>tech.fengjian</groupId>
              <artifactId>shopping-parent</artifactId>
              <version>1.0-SNAPSHOT</version>
              <type>pom</type>
              <scope>import</scope>
          </dependency>
      </dependencies>
  </dependencyManagement>
  ```

* 插件的继承也遵循同依赖继承一样的规则（pluginManagement），不在赘述

### 7.3 聚合和继承的关系

基于前面的内容,可以了解到,多模块 Maven 项目中的聚合与继承其实是两个概念,其目的完全是不同的。前者主要是为了方便快速构建项目,后者主要是为了消除重复配置。
对于聚合模块来说,它知道有哪些被聚合的模块,但那些被聚合的模块不知道这个聚合模块的存在。
对于继承关系的父POM来说,它不知道有哪些子模块继承于它,但那些子模块都必须知道自己的父 POM 是什么。
如果非要说这两个特性的共同点,那么可以看到,要聚合 POM 与继承关系中的父 POM 的 packaging 都必须是 pom,同时,聚合模块与继承关系中的父模块除了 POM 之外都没有实
际的内容。

在现有的实际项目中,我们往往会发现一个 POM 既是聚合 POM,又是父 POM,这么做主要是为了方便。一般来说,融合使用聚合与继承也没有什么问题。

Maven 默认能识别的父模块位置,所以当父子模块的目录关系也是父子目录时，不再需要配置 relativePath。

## 8. 实践

### 8.1 约定优于配置

maven 的项目约定：

源码目录为 src/main/java/
编译输出日录为 target/classes/
打包方式为 jar
包输出目录为 target/

### 8.2 maven 项目的构件顺序

实际的构建顺序是这样形成的:Maven 按序读取 POM,如果该 POM 没有依赖模块,那么就构建该模块,否则就先构建其依赖模块,如果该依赖还依赖于其他模块,则进一步先构建依赖的依赖换。

聚合项目之间往往没有依赖关系，按顺序。

父子项目之间，子项目继承（依赖）父项目，先构建父项目，然后再构建子项目。（这也就是如果单独执行子项目的 maven 命令往往会报错的原因，没有设置 relativePath 也没用 install 父项目到本地仓库）

### 8.3 使用 IDEA 创建一个 maven 项目

1. 不使用骨架（Archetype） 创建（`tips：` IDEA 版本不同，功能项位置可能会变化（演示版本为：2022.1.4））

   ![](https://s3.bmp.ovh/imgs/2023/02/02/cc216f8f2baf0aa8.png)



2. 使用骨架（Archetype）创建，选择骨架 `maven-archetype-quickstart`

   ![image-20230202123100232](https://s3.bmp.ovh/imgs/2023/02/02/28b39021de8f3575.png)



3. maven 项目标准目录结构（约定优于配置）

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

     

### 8.4 maven 核心 pom 文件

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

### 8.5 maven 生命周期

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

### 8.6 常用的 maven 命令

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



### 8.7  jar 包管理



#### 8.7.1 如何添加项目需要的 jar 包

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

   

#### 8.7.2 如何使用 maven 运行一个单元测试

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

#### 8.7.3 如何建立一个 web 应用

1. 从 Archetype 新建 web 项目

   ![image-20230207190017174](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207190017174.png)

2. 项目目录结构

   ![image-20230207190217734](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207190217734.png)

3. 新建 java 文件和 resources 文件

   ![image-20230207190814764](C:/Users/zhulu/AppData/Roaming/Typora/typora-user-images/image-20230207190814764.png)

   

#### 8.7.4 如何导入第三方 jar 包到本地仓库

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

#### 8.7.5 常用的 maven 插件

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

## 9. 总结

至此，本文已经提供了 maven 详细指南。通过这个教程，我们可以轻松使用 maven 地构建自己的 Maven java项目，方便团队内部开发和组织内共享代码。如果有任何问题，请在评论区留言。
