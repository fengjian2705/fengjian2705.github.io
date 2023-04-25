---
title: Nexus 搭建 Maven 私有仓库
tags: 
    - maven
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_init.jpg
# excerpt: 搭建自己的 maven 私有仓库
categories:
    - 后端
    - maven
---
> 阅读提示：本文内容是基于<font color="red">nexus-3.19.1-01</font>版本！！！

## 1. 私有仓库

### 1.1 什么是私有仓库？

私有仓库是一种特殊的远程仓库,它是架设在局域网内的仓库服务,私有仓库代理互联网上的远程仓库,供局域网内的 Maven 用户使用。当 Maven 需要下载项目需要的依赖的时候,它向私有仓库请求,如果私有仓库上不存在该依赖,则从外部的远程仓库下载,缓存在私服上之后,再为 Maven 的下载请求提供服务。此外,一些无法从外部仓库下载到的依赖也能从本地上传到私有仓库上供内部使用,如下图所示：



![](https://s3.bmp.ovh/imgs/2023/04/24/9a3f21f187ac0a19.png)

### 1.2 常用的构建私服的技术

1. Apache Archiva  
2. JFrog Artifactory
3. <font color="red">Sonatype Nexus</font>：也就是本文要介绍使用的搭建 maven 私服的软件[官网](https://www.sonatype.com/)

## 2. 快速起步

### 2.1 下载并解压

![](https://s3.bmp.ovh/imgs/2023/04/24/369d6e28beed20f7.png)

![](https://s3.bmp.ovh/imgs/2023/04/24/87f61e3e18c7bbb4.png)

### 2.2 运行

1. 切换到 nexus-3.19.1-01 的 bin 目录下

* mac/linux平台执行命令: `./nexus run`

* windows平台执行命令:`nexus /run`

**<font color="red">tips</font>**: 

* run 是前台启动，可以看到实时的启动日志，start 是后台启动；

* 启动如果遇到数据库（DB）相关的错误，可以删除 sonatype-work/nexus3/db 文件夹，重新启动；

* 还有一个问题是当前用户的权限不足，使用管理员角色运行即可



2. nexus 默认端口号 `8081` ，启动成功后可直接访问 http://localhost:8081/

* 新建仓库需登录后方可操作，默认账号 `admin`，密码存放路径 `/sonatype-work/nexus3/admin.password`  
* 登录成功后需修改密码

![](https://s3.bmp.ovh/imgs/2023/04/24/76adc1ac899eae3c.png)

## 3. Nexus 内置仓库

### 3.1 三种类型

- `proxy`: （代理）用来代理远程仓库，是介于远程仓库和本地仓库之间的私有仓库
  - 依赖存储策略：release，只会下载和存中央仓库中的发布版本依赖

- `group`: （仓库组）对我们的仓库进行分组管理
  - 依赖存储策略：release，只会下载和存中央仓库中的发布版本依赖

- `hosted`：（宿主）用于发布我们本地开发的项目，将项目发布到 hosted 仓库后再进行相关的部署工作
  - 依赖存储策略，release：用于存储稳定版本项目，snapshot: 用于发布快照版本软件

### 3.2 内置仓库

 Nexus内置了多个仓库，我们关心的是其中的 maven 仓库，NuGet 是微软推出的一种用于.NET开发的包管理工具，类似于Maven、npm等，这里按下不表。

![](https://s3.bmp.ovh/imgs/2023/04/24/8ad042040fe2b24a.png)

* maven-central:（proxy类型） 用于代理远程仓库，缓存远程仓库中的软件包，减少网络传输开销

  ![](https://s3.bmp.ovh/imgs/2023/04/25/fd5eb2197e7d9d4c.png)

* maven-release: （hosted类型）用于在本地存储发布的 release 版软件包，可以通过该仓库发布自己的软件包（通过设置 pom 文件中的 version，然后通过 maven 的 deploy 命令）

* maven-snapshots: （hosted类型）用于在本地存储发布的 snapshots 版软件包，可以通过该仓库发布自己的软件包（通过设置 pom 文件中的 version，然后通过 maven 的 deploy 命令）

* maven-public:（group类型） 将多个proxy仓库、hosted仓库和其他group仓库合并成一个虚拟仓库，在使用时就像一个单独的仓库

  相比较 proxy 和 hosted 两种类型仓库，创建 group 仓库时需指定成员仓库

  ![](https://s3.bmp.ovh/imgs/2023/04/25/82f0a4c9f52f8348.png)

## 4. 自定义私有仓库



![三种类型仓库](https://s3.bmp.ovh/imgs/2023/04/24/24ec67552f7f2482.png)

1. 创建一个代理阿里云仓库的私有仓库(proxy类型)：my_nexus

![](https://s3.bmp.ovh/imgs/2023/04/25/8196784d808db409.png)

2. 创建一个发布版本项目仓库：release  版本(hosted类型)

![](https://s3.bmp.ovh/imgs/2023/04/25/59413c16046f272f.png)

3. 创建一个快照版本项目仓库：snapshot 版本(hosted类型)

![](https://s3.bmp.ovh/imgs/2023/04/25/6e1dfe96dd05e7a7.png)

4. 创建一个 group 类型仓库

![image-20230425135352964](https://s3.bmp.ovh/imgs/2023/04/25/69d2929dd0557abb.png)

## 5. 自定义私有仓库的使用

创建是为了使用，接下来让我们看看如何将新创建好的私有仓库应用到项目中：

### 5.1 代理仓库的使用

1. 在私有仓库列表选择一个要使用的私有仓库，拷贝地址备用 

   http://localhost:8081/repository/fengjian-maven-central/

2. 可以在 setting.xml 文件中 或在 pom.xml 文件中添加私有仓库：
   该例配置中的releases和snapshots元素比较重要,它们用来控制Maven对于发布版构件
   和快照版构件的下载。这里需要注意的是enabled子元素,该例中releases 的enabled值为true,表示开启JBoss仓库的发布版本下载支持,
   而snapshots的enabled值为false,表示关闭 fengjian-maven-central 仓库的快照版本的下载支持。因此,根据
   该配置,Maven只会从 fengjian-maven-central 仓库下载发布版的构件,而不会下载快照版的构件。

```xml
<repositories>
  <!-- 配置私有仓库 -->
  <repository>
    <id>fengjian-maven-central</id>
    <name>fengjian-maven-central</name>
    <url>http://localhost:8081/repository/fengjian-maven-central/</url>
    <releases>
      <enabled>true</enabled>
    </releases>
    <snapshots> 
      <enabled>false</enabled>
    </snapshots>
  </repository>
</repositories>
```

### 5.1 部署仓库的使用

![](https://s3.bmp.ovh/imgs/2023/04/25/c1987f084eacf457.png)

配置项目部署的远程仓库：release 版本 & snapshot 版本

```xml
<distributionManagement>
  <repository>
    <id>fengjian-maven-release</id>
    <name>fengjian-maven-releasee</name>
    <url>http://localhost:8081/repository/fengjian-maven-release/</url>
  </repository>
  <snapshotRepository>
    <id>fengjian-maven-snapshots</id>
    <name>fengjian-maven-snapshots</name>
    <url>http://localhost:8081/repository/fengjian-maven-snapshots/</url>
  </snapshotRepository>
</distributionManagement>
```
**<font color="red">tips</font>**:release 和 snapshots 对应的 pom.xml 设置

```xml
<!-- 快照版本 SNAPSHOT 结尾 -->
<version>1.0-SNAPSHOT</version>

<!-- 稳定版本，非 SNAPSHOT 结尾-->
<version>2.0-RELEASE</version>
```





<font color="red">部署提示 401，依赖部署到远程仓库需要配置认证信息 </font>  

解决方法：  

在 maven 的 setting.xml 的 <servers> 标签下添加要认证的服务器信息，填写上对应的私有仓库用户名和密码即可

```xml
<servers>
	<server>
  	<id>fengjian-maven-release</id>
  	<username>admin</username>
  	<password>admin123</password>
  </server>
  <server>
    <id>fengjian-maven-snapshots</id>
    <username>admin</username>
    <password>admin123</password>
</server>
</servers>
```

---

<center>[本文完]</center>

