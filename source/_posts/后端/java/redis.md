---
title: redis
tags:
  - redis
  - 缓存
index_img: https://s3.bmp.ovh/imgs/2022/12/13/09030a142dab744d.jpg
# excerpt: 搭建自己的 maven 仓库
categories:
  - 后端
  - redis
---
## 一、安装与配置 redis

### 1. 下载安装包
[下载地址](https://redis.io/download/)

### 2. 上传到 linux 服务器并解压缩

```shell
   tar -zxvf redis-5.0.5.tar.gz 
```

### 3. 安装依赖

```shell
   yum install gcc-c++
```

### 4. 编译

```shell
   make
```

编译成功：Hint: It's a good idea to run 'make test' ;)

### 5. 安装

```shell
   make install
```

### 6. 配置

   * 进入 utils 目录，拷贝 redis 启动脚本
      ```shell
        cp redis_init_script /etc/init.d/ 
      ```
   * 修改 redis 核心配置文件 `redis.conf`

      - 拷贝配置文件
      ```shell
        mkdir /usr/local/redis
        
        cp redis.conf /usr/local/redis/
     
        cd /usr/local/redis
     
        vim redis.conf
      ```
     
     - 修改相关配置
       |参数        |默认值        |描述       |推荐设置                  |
       |-----------|-------------|----------|-------------------------|
       |daemonize  |no           |是否后台启动 |yes                     |
       |dir        |./           |工作目录    |/usr/local/redis/working|
       |bind       |127.0.0.1    |绑定的ip    |0.0.0.0                 |
       |requirepass|foobared     |访问密码    |自定义即可                |
       |port       |6379         |端口号      |6379                   |
     
     - 修改启动脚本 `redis_init_script`
       |参数        |默认值                              |描述                 |推荐设置                    |
       |-----------|-----------------------------------|--------------------|---------------------------|
       |REDISPORT  |6379                               |端口号               |若要修改去 redis.conf中修改即可|
       |CONF       |CONF="/etc/redis/${REDISPORT}.conf"|启动指定的配置文件      |/usr/local/redis/redis.conf|
      
      - 赋权限
        ```shell
          chmod 777 redis_init_script
        ```
      - 启动
        ```shell
          ./redis_init_script start
        ```
      - 停止
        ```shell
          ./redis_init_script stop
        ```
      - 设置 redis 开机自启动
        ```shell
          vim redis_init_script
          
          # 新增脚本 
          # !/bin/bash
          # chkconfig: 22345 10 90
          # description: Start and Stop redis
          
          # 注册开机自启动
          chkconfig redis_init_script on 
        
          # 重启测试
          reboot
        ```
   
       
     
      
      





## 二、redis 持久化机制

### 1. RDB

> Redis DataBase 

1. RDB 概念：每隔一段时间，把内存中的数据写入磁盘的临时文件，作为快照，恢复的时候把快照文件读进内存。如果宕机重启，那么内存里的数据肯定会没有的，那
dis后，则会恢复。

2. 备份与恢复： 内存备份 --> 磁盘临时文件 临时文件 --> 恢复到内存

3. RDB优劣势
    * 优势
      1. 每隔一段时间备份，全量备份
      2. 灾备简单，可以远程传输
      3. 子进程备份的时候，主进程不会有任何io操作（不会有写入修改或删除），保证备份数据的的完整性
      4. 相对AOF来说，当有更大文件的时候可以快速重启恢复
    * 劣势
      1. 发生故障是，有可能会丢失最后一次的备份数据
      2. 子进程所占用的内存比会和父进程一模一样，如会造成CPU负担
      3. 由于定时全量备份是重量级操作，所以对于实时备份，就无法处理了。
      
4. RDB的配置
   * 保存位置，可以在redis.conf自定义： 
     dbfilename dump.rdb
     dir /user/local/redis/working
   * 保存机制：
   ```shell
    save 900 1
    save 300 10
    save 60 10000
    save 10 3
   ```
   ```shell
    * 如果1个缓存更新，则15分钟后备份
    * 如果10个缓存更新，则5分钟后备份
    * 如果10000个缓存更新，则1分钟后备份
    * 演示：更新3个缓存，10秒后备份
    * 演示：备份dump.rdb，删除重启
   ```
   * stop-writes-on-bgsave-error
      yes：如果save过程出错，则停止写操作
      no：可能造成数据不一致
   * rdbcompression
     yes：开启rdb压缩模式
     no：关闭，会节约cpu损耗，但是文件会大，道理同nginx
   * rdbchecksum
     yes：开启校验，性能损耗 10%
     no：关闭，节约性能


### 2.AOF

> Append Only File

1. 引子：RDB会丢失最后一次备份的rdb文件，但是其实也无所谓，其实也可以忽略不计，毕竟是缓存，丢了就丢了，但是如果追求数据的完整性，那就的考虑使用AOF了

2. AOF特点
   * 以日志的形式来记录用户请求的写操作。读操作不会记录，因为写操作才会存存储。
   * 文件以追加的形式而不是修改的形式。
   * redis的aof恢复其实就是把追加的文件从开始到结尾读取执行写操作。

3. 优势
   * AOF更加耐用，可以以秒级别为单位备份，如果发生问题，也只会丢失最后一秒的数据，大大增加了可靠性和数据完整性。所以AOF可
      一次，使用fsync操作。
   * 以log日志形式追加，如果磁盘满了，会执行 redis-check-aof 工具
   * 当数据太大的时候，redis可以在后台自动重写aof。当redis继续把日志追加到老的文件中去时，重写也是非常安全的，不会影响客户端
      作。
   * AOF 日志包含的所有写操作，会更加便于redis的解析恢复。

4. 劣势
   * 相同的数据，同一份数据，AOF比RDB大
   * 针对不同的同步机制，AOF会比RDB慢，因为AOF每秒都会备份做写操作，这样相对与RDB来说就略低。 每秒备份fsync没毛病，但是
      的每次写入就做一次备份fsync的话，那么redis的性能就会下降。
   * AOF发生过bug，就是数据恢复的时候数据不完整，这样显得AOF会比较脆弱，容易出现bug，因为AOF没有RDB那么简单，但是呢为
      的产生，AOF就不会根据旧的指令去重构，而是根据当时缓存中存在的数据指令去做重构，这样就更加健壮和可靠了。

5. AOF的配置
   ```shell
    # AOF 默认关闭，yes可以开启
    appendonly no
    # AOF 的文件名
    appendfilename "appendonly.aof"
    # no：不同步
    # everysec：每秒备份，推荐使用
    # always：每次操作都会备份，安全并且数据完整，但是慢性能差
    appendfsync everysec
    # 重写的时候是否要同步，no可以保证数据安全
    no-appendfsync-on-rewrite no
    # 重写机制：避免文件越来越大，自动优化压缩指令，会 fork 一个新的进程去完成重写动作，新进程里的内存数据会被重写，此时
    # 当前 AOF 文件的大小是上次 AOF 大小的 100% 并且文件体积达到 64m，满足两者则触发重写
    auto-aof-rewrite-percentage 100
    auto-aof-rewrite-min-size 64mb
   ```

### 3.到底采用RDB还是AOF呢？

1. 如果你能接受一段时间的缓存丢失，那么可以使用RDB
2. 如果你对实时性的数据比较care，那么就用AOF
