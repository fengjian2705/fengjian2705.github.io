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
## 背景

> 企业内部为了方便统一管理依赖而搭建的 web 服务器

![maven 仓库的架构图](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_002.jpg)


常用构建技术：Apache Archiva JFrog Artifactory <font color="red">Sonatype Nexus</font>

## 搭建步骤

### 下载软件包

[官网地址](https://www.sonatype.com/)

![不同版本的安装包](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_003.jpg)

### 解压并运行

![解压后的文件夹](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_004.jpg)

切换到 nexus-3.19.1-01 的 bin 目录下：

mac/linux:
```bash
./nexus run
```
windows:
```bash
nexus /run
```
nexus 默认端口号 `8081`  
新建仓库需登录后方可操作，默认账号 `admin`，密码存放路径 `/sonatype-work/nexus3/admin.password`  
登录成功后可以修改密码

![nexus首页](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_005.jpg)

## Nexus 内置仓库

- proxy: 用来代理远程仓库，是我们远程人仓库和本地仓库中间的私有仓库
- group: 对我们的仓库进行分组管理
- hosted：用于发布我们本地开发的项目，将项目发布到 hosted 仓库后再进行相关的部署工作
  - release：用于发布稳定版本软件
  - snapshot: 用于发布快照版本软件  

发布版本的 release 和 snapshot 对应 pom 文件中的 version 信息
```xml
<!-- 快照版本 -->
<version>1.0-SNAPSHOT</version>

<!-- 稳定版本-->
<version>2.0-RELEASE</version>
```

![nexus内置仓库](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_006.jpg)

## 创建仓库

> 我们主要使用 maven2 的三种类型仓库

![maven2仓库](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_007.jpg)

1. 创建一个针对中央仓库的私有仓库：my_nexus

![my_nexus代理中央仓库](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_008.jpg)

2. 创建一个发布仓库：release 版本

![release版本](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_012.jpg)

3. 创建一个快照版本仓库：snapshot 版本

![snapshot版本](https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/nexus_010.jpg)

## 仓库的使用

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
项目发布不同版本，查看依赖上传的仓库

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

