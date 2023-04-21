---
title: JDK 动态代理
tags: 
    - elasticsearch
    - 搜索引擎
    - lucene
index_img: https://cdn.jsdelivr.net/gh/fengjian2705/cdn/img/proxy/proxy01.jpg
# excerpt: JDK 动态代理
categories:
  - 后端
  - elasticsearch
---
## 一、分布式搜索引擎

### 1. 搜索引擎

1. 数据源：数据库 爬虫爬取 -> 搜索引擎节点上

2. 提供检索服务，检索之后，信息展示到页面上

3. 优化用户体验，定制化推荐，网络营销策略


### 2. 分布式

多个节点共同组成的一个搜索服务，提升可扩展性、存储容量


## Lucene vs Solr vs Elasticsearch

1. 倒排索引

2. Lucene 是类库，基于 java 的全文搜索引擎，不是应用程序，是代码库或者叫 api，就是一个 jar 包，仅限 java 整合，其他语言不方便

3. Solr 基于 Lucene 构建的开源引擎，独立部署到容器中，可以实现集群，通过 zookeeper，可靠性、可扩展性、容错性都不错

4. Elasticsearch 基于 Lucene 的分布式搜索引擎，提供了一些 restful 风格的 api，可以提供近实时的搜索服务，支持 PB（1024T） 级别的搜索，  
   github 就由 Solr 更改为 Elasticsearch，Elasticsearch 还可以提供大数据的分析
   

## ES 核心术语

1. 索引 index         -> 表
2. ~~类型 type~~      -> 表的逻辑类型 
3. 文档 document      -> 行
4. 字段 fields        -> 列

tips: 7.x 之后 type 就废弃不用了

比如 stu 表一行记录数据，ES 的表述为：
```json
  stu_index
  {
    id: 1,
    name: jack,
    age: 18
  } 
```
5. 映射 mapping      -> 表结构定义
6. 近实时 NRT        -> near real time
7. 节点 node         -> 每一个服务器
8. shard replica    -> 数据分片与备份








