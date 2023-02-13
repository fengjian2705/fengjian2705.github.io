---
title: 分布式搜索引擎-ElasticSearch
tags: 
    - 搜索引擎
    - ElasticSearch
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/proxy/proxy01.jpg
# excerpt: JDK 动态代理
categories:
  - 后端
  - 搜索引擎
---

## 1. 如果不用会怎么样

目前搜索的弊端（直接查数据库）：

* 空格支持 <font color="red">x</font>
* 拆词查询 <font color="red">×</font>
* 搜索内容高亮 <font color="red">× </font>
* 海量数据查询 <font color="red">×</font>

## 2. 什么是分布式搜索引擎

### 2.1 概念

1. 首先是搜索引擎
2. 其次支持分布式存储与搜索

### 2.2 Lucene vs Solr  vs ElasticSearch

1. Lucene 是类库：

   * 基于 java 的全文搜索引擎，它不是一个应用程序，它是一个代码库或者是一些 api 供我们调用的，本质上就是一个 jar 包，可以直接被引用（导入）到项目中去，然后调用它的 api 进行搜索集成
   * 只能使用 java 进行整合，水平扩展集群的话比较复杂

2. Solr 是基于 Lucene 构建的开源搜索引擎：

   * 本质上是对 Lucene 进行了一层封装，是 Apache 的一个开源项目，使用 java 开发的，可以独立部署到 Tomcat 或 jetty 中
   * 可以通过 zookeeper 实现集群部署
   * 提供分布式索引、负载均衡、自动故障转移恢复，可靠性、可扩展性和容错性都挺好

3. ElasticSearch 也是基于 Lucene：

   * 分布式搜索引擎

   * restful 风格接口，支持不同开发语言调用

   * 近实时的搜索服务

   * 支持 PB（1024 T） 级别的搜索

   * 大数据的分析

     

## 3. ES 核心术语

1. 索引 index：		表
2. <del>类型 type：         表逻辑类型</del>
3. 文档 document：行
4. 字段 fields：        列
4. 映射 mapping      表结构定义
4. 近实时 NRT         Near real time
4. 节点 node           每一台服务器都是一个节点
4. shard replica       数据分片与备份

`提示：` type 已在 ES 7.x 后废弃！



## 4. 什么是倒排索引

需求：在 30 份文档中找出其中包含“张三”的文档，通常来说我们会根据把文档排序，然后依次遍历来判断当前遍历到的文档中是否包含“张三”，这种查找方式就是根据“文档id” -> "文档内容"的对应关系依次判断文档内容是否包含“张三“，如果存在一张”张三“->"文档"的映射关系，我们可以直接根据张三来获取到满足条件的文档，这就是两种不同的方式进行索引：

正序：根据文档来确定文档中的内容

倒排：文档中的内容到文档的映射，这种就叫做倒排索引

## 5. 安装 ElasticSearch

1. 下载安装包，[传送门](https://www.elastic.co/cn/downloads/elasticsearch)

2. 上传安装包到 linux 服务器，目录 `/home/software`

3. 解压文件到目录`/usr/local`，tar -zxvf xxx.tar 

4. es文件目录结构：

   * **bin**：可执行文件，例如启动 es 的脚本文件就在这里
   * **config**：配置问价存放位置
     * elasticsearch.yml：核心配置文件
     * jvm.options
     * log4j2.properties
     * role_mapping.yml
     * roles.yml
     * users
     * users_rolse
   * **jdk**：依赖的 java 环境
   * **lib**：依赖的一些类库
   * LINCENSE.txt
   * **logs**：日志文件
   * **modules**：跟 es 有关的模块
   * NOTICE.txt
   * **plugins**：插件存放位置，包括自定义的
   * README.textile

5. 在 es 文件目录中新建 data 目录，数据目录，后续的索引存放位置

6. 修改核心配置文件 **elasticsearch.yml**

   ```yam
   # 集群名称
   cluster.name: fengjian-elasticsearch
   
   # 节点名称
   node.name: es-node1
   
   # 数据路径
   path.data: /usr/local/elasticsearch-7.4.2/data
   
   # 日志路径
   path.logs: /usr/local/elasticsearch-7.4.2/logs
   
   # 网络地址绑定
   network.host: 0.0.0.0
   
   # http 端口号
   http.port: 9200
   
   # 集群启动时初始化节点列表
   cluster.initial_master_nodes:["es-node1"]
   
   ```

7. 修改 **jvm.options** 配置项

   ```yam
   # Xms 初始化堆空间
   # Xmx 最大堆空间
   -Xms128m
   -Xmx128m
   ```

8. 启动 ElasticSearch

   * `提示：`：root 用户是无法启动 es 的，需要创建一个新用户！	

     ```she
     # 添加用户
     useradd esuser
     
     # 授权 1
     chown -R esuser /usr/local/elasticsearch-7.4.2
     
     # 切换用户
     su esuser
     
     # 授权 2，elasticsearch.keystore accessDenied!
     chown -R esuser:esuser /usr/local/elasticsearch-7.4.2
     
     
     ```

   * 启动，./elasticsearch 或 ./elasticsearch（后台启动）

     * 异常 1：max file descriptors [4096]  -> [65535]
     * 异常 2：max number of threads [3756]  -> [4096]
     * 异常 3：max virtual memory areas vm.max_map_count [65530]  -> [262144]

   * 修改配置 vim /etc/security/limits.conf，文件最后新增如下：

     ```shell
     * soft nofile 65536
     * hard nofile 131072
     * soft nproc 2048
     * hard nproc 4096
     ```

   * 修改配置文件 vim /etc/sysctl.conf，文件最后新增如下：

     ```she
     vm.max_map_count=262145
     ```

     编译文件：sysctl -p

   * 启动成功，浏览器中访问 192.168.12.23:9200 查看信息
   * 关闭的话，jps elasticsearch 查看进程 id 后 kill id

   **提示**：如果在网页上显示localhost未发送任何数据

   ```yam
   xpack.security.enable: false
   
   xpack.security.http.ssl:
   	enabled: false
   ```

   

## 6. 安装 elasticsearch-head 插件

1. chrome 中谷歌市场中搜索 elasticsearch 进行安装即可

   ![](https://s3.bmp.ovh/imgs/2023/02/13/55aface0b2e180da.png)

2.  基本操作

   * 新建索引：5 个分片，0 个副本

     ![image-20230213220330884](https://s3.bmp.ovh/imgs/2023/02/13/eb62784a1325edd7.png)

   * 新建索引：5 个分片，1 个副本

     ![image-20230213220842070](https://s3.bmp.ovh/imgs/2023/02/13/87b4fb190d3dd239.png)

   * 观察副本数与分片数，图中灰色框代表未分配副本分片

     ![](https://s3.bmp.ovh/imgs/2023/02/13/7e1fa1d9c6794e98.png)



## 番外：mac 多机器克隆修改项



1. 安装虚机，启动进入
2. ipaddr 搜索 link/ether 拷贝 Mac 地址备用
3. 修改 /etc/udev/rules.d/70-persistent0ipoib.rules
   * 修改 ACTION=="add" 这一行 address，粘贴 mac 地址，保存
4. 修改ip地址，打开  /etc/sysconfig/network-scripts/ifcfg-ens33 
   * IPADDR 改成网段的任意静态 ip 即可
   * HWADDR="" 改成备用的物理地址
5. 重启计算机或者使用 service network restart
6. 验证 ipaddr
7. 修改虚机名和 secureCRT 连接名







 