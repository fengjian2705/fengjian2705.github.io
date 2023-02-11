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









 