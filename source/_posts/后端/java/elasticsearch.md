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

   **提示**：如果在网页上显示localhost未发送任何数据，修改配置文件如下：

   ```yam
   xpack.security.enable: false
   
   xpack.security.http.ssl:
   	enabled: false
   ```

   

## 6. 安装 elasticsearch-head 插件

### 6.1 chrome 中谷歌市场中搜索 elasticsearch 进行安装即可

   ![](https://s3.bmp.ovh/imgs/2023/02/13/55aface0b2e180da.png)

### 6.2  head 与 postman 基本操作

   1. 版本信息查询

      ![image-20230215103432717](https://s3.bmp.ovh/imgs/2023/02/15/aeaf0be9dec3cb47.png)

      ```json
      GET http://192.168.X.X:9200/

   2. 集群健康查询

      ![image-20230215101655801](https://s3.bmp.ovh/imgs/2023/02/15/d0972558c7fcc4b8.png)

      ```json
      GET http://192.168.X.X:9200/_cluster/health
      ```

   3. 新建索引：5 个分片，0 个副本

      ![image-20230215131749500](https://s3.bmp.ovh/imgs/2023/02/15/8d31a4573d3d9e8b.png)

      ![](https://s3.bmp.ovh/imgs/2023/02/15/d611cd6d6a89bd5f.png)

      ```json
      PUT http://192.168.x.x:9200/index_demo
      
      {
          "settings":{
              "number_of_shards":5,
              "number_of_replicas":0
          }
      }
      ```

      

4. 新建索引：5 个分片，1 个副本，未分配分片有 5 个

   ![image-20230215135509191](https://s3.bmp.ovh/imgs/2023/02/15/d22f2d842d3e9f41.png)

   ```json
   PUT http://192.168.x.x:9200/index_123
   
   {
       "settings":{
           "number_of_shards":5,
           "number_of_replicas":1
       }
   }
   ```

   

5. 删除索引：

   ![image-20230215141019148](https://s3.bmp.ovh/imgs/2023/02/15/3fe5fe90dce805d4.png)

   ```json
   DELETE http://192.168.x.x:9200/index_123
   ```

6. 查询索引

   ```json
   GET http://192.168.x.x:9200/index_temp
   
   {
       "index_temp": {
           "aliases": {},
           "mappings": {},
           "settings": {
               "index": {
                   "routing": {
                       "allocation": {
                           "include": {
                               "_tier_preference": "data_content"
                           }
                       }
                   },
                   "number_of_shards": "3",
                   "provided_name": "index_temp",
                   "creation_date": "1676441762678",
                   "number_of_replicas": "0",
                   "uuid": "ryzEGgNIS3ycfWfV-lhaOQ",
                   "version": {
                       "created": "7160099"
                   }
               }
           }
       }
   }
   ```

7. 查询全部索引概览

   ```json
   GET http://192.168.147.132:9200/_cat/indices?v
   
   health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
   green  open   .geoip_databases aqUccqm1RzCvrVBSqK6xfg   1   0         41           36     42.3mb         42.3mb
   green  open   index_demo       h7rxnFLDTkOcGreaIBIiEw   5   0          0            0      1.1kb          1.1kb
   green  open   index_temp       ryzEGgNIS3ycfWfV-lhaOQ   3   0          0            0       678b           678b
   
   ```

## 7. mappings 索引数据结构定义

### 7.1 新建索引并设置 mapping 

```json
PUT http://192.168.147.132:9200/index_mapping

{
    "mappings": {
        "properties": {
            "realname": {
                "type": "text",
                "index": true
            },
            "username": {
                "type": "keyword",
                "index": false
            }
        }
    }
}
```

realname：字段名	type：类型	index：是否被索引

|  type   |        描述        |
| :-----: | :----------------: |
|  text   |  字符串，可被分词  |
| keyword | 字符串，不可被分词 |
|  long   |       长整型       |
| integer |        整型        |
|  float  |       浮点型       |
|  true   |       布尔型       |
|  byte   |        字节        |
| boolean |       短整型       |
|  date   |      日期类型      |
| object  |      对象类型      |
| double  |       浮点数       |
| [1,2,3] |      数组类型      |



index：是否被索引（查询）

keyword：无法被分词

### 7.2 索引分词

```json
GET http://192.168.147.132:9200/index_mapping/_analyze

{
    "field": "username",
    "text": "good good study,day day up!"
}

{
    "tokens": [
        {
            "token": "good good study,day day up!",
            "start_offset": 0,
            "end_offset": 27,
            "type": "word",
            "position": 0
        }
    ]
}
```

### 7.3 新增 mapping 中 properties

```json
POST http://192.168.147.132:9200/index_mapping/_mapping
{
    "properties": {
        "id": {
            "type": "long",
            "index": true
        },
        "age": {
            "type": "integer",
            "index": false
        }
    }
}
```

## 8. 文档的基本操作

### 8.1 新建索引 my_doc，分片 1，副本 0

```sql
PUT http://192.168.x.x:9200/my_doc

{
    "settings":{
        "number_of_shards":1,
        "number_of_replicas":0
    }
}
```

### 8.2 新增文档信息

```sql
POST http://192.168.147.132:9200/my_doc/_doc/1

{
    "id":1001,
    "name":"jack",
    "age":18
}

// 新增成功
{
    "_index": "my_doc",
    "_type": "_doc",
    "_id": "FCRSVYYB_DJayXIYu1rV",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 1,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 6,
    "_primary_term": 1
}

// 1 是文档 id，可自定义，可系统生成，通过 es-head 查看文档

"mappings": {
    "_doc": {
        "properties": {
            "name": {
                "type": "text",
                "fields": {
                    "keyword": {
                        "ignore_above": 256,
                        "type": "keyword"
                    }
                }
            },
            "id": {
                "type": "long"
            },
            "age": {
                "type": "long"
            }
        }
    }
}
```

### 8.3 删除文档

```sql
DELETE http://192.168.X.X:9200/my_doc/_doc/2
```

### 8.4 修改文档-局部修改

```sql
UPDATE http://192.168.X.X:9200/my_doc/_doc/1/_update

{
    "doc":{
        "name":"春风得意马蹄疾啊" // 指定修改内容
    }
}
```

### 8.5 修改文档-全量修改

```sql
PUT http://192.168.X.X:9200/my_doc/_doc/1

{
    "id": 1005,
    "name": "春风得意马蹄疾",
    "age": 20
}
```

### 8.6 查询文档根据文档 id

```sql
GET http://192.168.x.x:9200/my_doc/_doc/1

{
    "_index": "my_doc",
    "_type": "_doc",
    "_id": "1",
    "_version": 5,
    "_seq_no": 11,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "id": 1005,
        "name": "春风得意马蹄疾",
        "age": 20
    }
}
```

### 8.7 查询全部

```sql
GET http://192.168.x.x:9200/my_doc/_doc/_search

{
    "took": 4,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 4,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "my_doc",
                "_type": "_doc",
                "_id": "3",
                "_score": 1.0,
                "_source": {
                    "id": 1003,
                    "name": "风间",
                    "age": 19
                }
            },
            {
                "_index": "my_doc",
                "_type": "_doc",
                "_id": "FCRSVYYB_DJayXIYu1rV",
                "_score": 1.0,
                "_source": {
                    "id": 1004,
                    "name": "一日看尽长安花",
                    "age": 20
                }
            },
            {
                "_index": "my_doc",
                "_type": "_doc",
                "_id": "FSRWVYYB_DJayXIY8Voz",
                "_score": 1.0,
                "_source": {
                    "id": 1005,
                    "name": "春风得意马蹄疾啊",
                    "age": 20
                }
            },
            {
                "_index": "my_doc",
                "_type": "_doc",
                "_id": "1",
                "_score": 1.0,
                "_source": {
                    "id": 1005,
                    "name": "春风得意马蹄疾",
                    "age": 20
                }
            }
        ]
    }
}
```

### 8.8 根据 id 查询，设置 source 查询字段，多个字段逗号隔开

```sql
GET http://192.168.x.x:9200/my_doc/_doc/1?_source=name

{
    "_index": "my_doc",
    "_type": "_doc",
    "_id": "1",
    "_version": 5,
    "_seq_no": 11,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "name": "春风得意马蹄疾"
    }
}
```

### 8.9 查询全部，设置 source 查询字段，多个逗号隔开

```sql
GET http://192.168.x.x:9200/my_doc/_doc/_search?source=name
```

### 8.10 HEAD 方式查询文档，根据状态码判断数据是否存在（节省传输效率）

``` sql
HEAD http://192.168.147.132:9200/my_doc/_doc/11

存在：200
不存在：404
```



### 8.11 文档乐观锁控制






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







 