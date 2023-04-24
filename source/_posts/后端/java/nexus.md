---
title: Nexus 私服搭建
tags: 
    - maven
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_init.jpg
# excerpt: 搭建自己的 maven 仓库
categories:
    - 后端
    - maven
---
## 1. Maven 私服

### 1.1 什么是私服？

私服是一种特殊的远程仓库,它是架设在局域网内的仓库服务,私服代理广域网上的远程仓库,供局域网内的Maven用户使用。当Maven需要下载我构件的时候,它从私服请求,如果私服上不存在该构件,则从外部的远程仓库下载,缓提存在私服上之后,再为Maven的下载请求提供服务。此外,一些无法从外部仓库下载到的构件也能从本地上传到私服上供大家使用,如下图所示。



![screenshot-20230424-162050](https://s3.bmp.ovh/imgs/2023/04/24/9a3f21f187ac0a19.png)

### 1.2 常用的构建私服的技术

1. Apache Archiva  
2. JFrog Artifactory
3. <font color="red">Sonatype Nexus</font>：也就是本文要介绍使用的搭建 maven 私服的软件[官网](https://www.sonatype.com/)

## 2. 快速起步

### 2.1 下载安装包

![不同平台的安装包](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_003.jpg)

### 2.2 解压运行

![解压后的文件夹](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_004.jpg)

1. 切换到 nexus-3.19.1-01 的 bin 目录下

* mac/linux平台执行命令:

```bash
./nexus run
```
* windows平台执行命令:

```bash
nexus /run
```
2. nexus 默认端口号 `8081`  
   新建仓库需登录后方可操作，默认账号 `admin`，密码存放路径 `/sonatype-work/nexus3/admin.password`  
   登录成功后可以修改密码

![nexus首页](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_005.jpg)

## 3. Nexus 内置三种仓库

- `proxy`: 用来代理远程仓库，是我们远程仓库和本地仓库中间的私有仓库
- `group`: 对我们的仓库进行分组管理
- `hosted`：用于发布我们本地开发的项目，将项目发布到 hosted 仓库后再进行相关的部署工作
  - release：用于发布稳定版本软件
  - snapshot: 用于发布快照版本软件  

![nexus内置仓库](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_006.jpg)

* pom 文件中发布版本的 release 和 snapshot 对应 pom 文件中的 version 信息

```xml
<!-- 快照版本 SNAPSHOT 结尾 -->
<version>1.0-SNAPSHOT</version>

<!-- 稳定版本，非 SNAPSHOT 结尾-->
<version>2.0-RELEASE</version>
```



## 4. 创建仓库



![maven2仓库](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_007.jpg)

1. 创建一个代理中央仓库的私有仓库(proxy类型)：my_nexus

![my_nexus代理中央仓库](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_008.jpg)

2. 创建一个发布版本仓库：release  版本(hosted类型)：release 版本

![release版本](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_012.jpg)

3. 创建一个快照版本仓库：snapshot 版本(hosted类型)

![snapshot版本](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_010.jpg)

## 5. 在项目中的使用私有仓库

> maven 项目中依赖的查找顺序 本地仓库 -> 私有仓库 -> 中央仓库

1. 添加私有仓库到 maven 项目中

![拷贝仓库地址](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_011.jpg)

pom.xml 文件：
```xml
<repositories>
  <!-- 配置私有仓库 -->
  <repository>
    <id>my nexus</id>
    <name>My Nexus</name>
    <url>http://localhost:8081/repository/my_nexus/</url>
    <releases>
      <enabled>true</enabled>
    </releases>
    <snapshots> 
      <enabled>true</enabled>
    </snapshots>
  </repository>
</repositories>
```

2. 配置发布版本仓库：release 版本 & snapshot 版本

```xml
<distributionManagement>
  <repository>
    <id>my release</id>
    <name>My Release</name>
    <url>http://localhost:8081/repository/my_realease/</url>
  </repository>
  <snapshotRepository>
    <id>my snapshot</id>
    <name>My Snapshot</name>
    <url>http://localhost:8081/repository/my_snapshot/</url>
  </snapshotRepository>
</distributionManagement>
```
项目发布不同版本到私有仓库，查看依赖上传的仓库

![发布项目](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_013.jpg)

<font color="red">发布提示 401，依赖上传仓库需要登录 </font>  

解决方法：  

在 maven 的 setting.xml 中设置登录 `Nexus` 的用户名和密码即可

```xml
<servers>
	<server>
  	<id>my release</id>
  	<username>admin</username>
  	<password>admin123</password>
  </server>
  <server>
    <id>my snapshot</id>
    <username>admin</username>
    <password>admin123</password>
</server>
</servers>
```

__完__

