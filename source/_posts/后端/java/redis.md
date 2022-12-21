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
        停止时会提示没有认证！  
        修改脚本文件如下：
        ```shell
          $CLIEXEC -a "密码" -p $REDISPORT shutdown
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
      3. 子进程备份的时候，主进程不会有任何 io 操作（不会有写入修改或删除），保证备份数据的的完整性
      4. 相对 AOF 来说，当有更大文件的时候可以快速重启恢复
    * 劣势
      1. 发生故障时，有可能会丢失最后一次的备份数据
      2. 子进程所占用的内存比会和父进程一模一样，如会造成 CPU 负担
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
      yes：如果 save 过程出错，则停止写操作
      no：可能造成数据不一致
   * rdbcompression
     yes：开启rdb压缩模式
     no：关闭，会节约 cpu 损耗，但是文件会大，道理同 nginx
   * rdbchecksum
     yes：开启校验，性能损耗 10%
     no：关闭，节约性能


### 2.AOF

> Append Only File

1. 引子：RDB 会丢失最后一次备份的 rdb 文件，但是其实也无所谓，其实也可以忽略不计，毕竟是缓存，丢了就丢了，但是如果追求数据的完整性，那就的考虑使用 AOF 了

2. AOF 特点
   * 以日志的形式来记录用户请求的写操作。读操作不会记录，因为写操作才会存存储。
   * 文件以追加的形式而不是修改的形式。
   * redis 的 aof 恢复其实就是把追加的文件从开始到结尾读取执行写操作。

3. 优势
   * AOF 更加耐用，可以以秒级别为单位备份，如果发生问题，也只会丢失最后一秒的数据，大大增加了可靠性和数据完整性。所以 AOF 可每秒同步
      一次，使用 fsync 操作。
   * 以 log 日志形式追加，如果磁盘满了，会执行 redis-check-aof 工具
   * 当数据太大的时候，redis 可以在后台自动重写 aof。当 redis 继续把日志追加到老的文件中去时，重写也是非常安全的，不会影响客户端
      作。
   * AOF 日志包含的所有写操作，会更加便于 redis 的解析恢复。

4. 劣势
   * 相同的数据，同一份数据，AOF 比 RDB 大
   * 针对不同的同步机制，AOF 会比 RDB 慢，因为 AOF 每秒都会备份做写操作，这样相对于 RDB 来说就略低。 每秒备份 fsync 没毛病，但是如果每次写入就做一次备份 fsync 的话，那么 redis 的性能就会下降。
   * AOF 发生过 bug，就是数据恢复的时候数据不完整，这样显得 AOF 会比较脆弱，容易出现 bug，因为 AOF 没有 RDB 那么简单，防止 bug 的产生，AOF 就不会根据旧的指令去重构，而是根据当时缓存中存在的数据指令去做重构，这样就更加健壮和可靠了。

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

### 3.到底采用 RDB 还是 AOF 呢？

1. 如果你能接受一段时间的缓存丢失，那么可以使用 RDB，可能会丢失最后一次备份数据
2. 如果你对实时性的数据比较 care，那么就用 AOF，最多损失 2 秒的数据（每秒备份）
3. 先加载 AOF，然后加载 RDB，如果 AOF 损坏，则删除掉后使用 RDB 恢复即可


## 三、redis 主从复制

### 1. 查看 replication 信息

```shell
   info replication
```

```shell
# Replication
role:master
connected_slaves:0
master_replid:989dec5a359aed864bfd5c14223403c7ccb5db11
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

### 2. 修改配置，设置为从节点

```shell
     # 设置主节点 ip port
     replicaof 192.168.88.135 6379
     
     # 设置主节点密码
     masterauth 123456
     
     # 设置从节点为只读（默认配置）
     replica-read-only yes
```
重启 redis 使之生效!

### 3. 无磁盘化复制

> 定义：master 创建一个进程写入 rdb 文件到内存而不是磁盘
> 场景：网络好，硬盘读写差的情况

开启配置：

```shell
   # 开启无磁盘主从同步
   repl-diskless-sync yes
   # 同步延迟，默认 5 秒
   repl-diskless-sync-delay 5
```

### 4. 哨兵机制 sentinel

> 作用：针对 redis 主从复制机器宕机处理

* 哨兵配置：
  - 拷贝配置文件
    ```shell
       cd /home/install/redis
       cp sentinel.conf /usr/local/redis/sentinel.conf
    ```
  - 进行相关配置
    ```shell
        # 关闭保护模式
          protected-mode no
        # 后台启动
          daemonize yes
        # 日志存放位置
          logfile /usr/local/redis/sentinel/redis-sentinel.log
        # 工作目录
          dir /usr/local/redis/sentinel
        # 监控的 master
          sentinel monitor master-135 192.168.88.135 6379 2
        # master 密码 
          sentinel auth-pass master-132 123456
        # 判断 master down 时间， 10 秒，默认 30 秒
          sentinel down-after-milliseconds master-132 10000
        # 并发同步数量，默认 1，某一个 slave 被选举为 master后，其他 slave 从新的 master 同步数据
          sentinel parallel-syncs master-132 1
        # 故障转移超时时间，默认 3 分钟
          sentinel failover-timeout master-132 180000
    ```
  - 拷贝 sentinel.conf 到其他服务器
    ```shell
       scp sentinel.conf root@192.168.88.135:/usr/local/redis/
    ```
  - 启动
    ```shell
       redis-sentinel sentinel.conf
    ```
  - SpringBoot 整合哨兵
    ```yaml
       spring:
          redis:
            #单机单实例
            #database: 1
            #host: 192.168.88.135
            #password: 123456
            #port: 6379
            #哨兵机制
            database: 1
            password: 123456
            sentinel:
              master: master-132
              nodes: 192.168.88.135:26379,192.168.88.136:26379,192.168.88.137:26379
    ```

## 四、redis 缓存过期机制 & 内存淘汰机制

### 缓存过期机制：设置过 expire 的 key 过期后

1. （主动）定期删除：定期定时检查

    ```shell
       # 每秒钟检查 10 次，1-500范围，过大对于 cpu 消耗大
         hz 10 
    ```

2. （被动）惰性删除：访问到的 key，若过期则会被删除

### 内存淘汰管理

1. 最大内存配置，超出则使用淘汰策略
    ```shell
        maxmemory <bytes>
    ```
2. 策略
   v 开头的都是针对设置过 expire 的 key。
   ```shell
      # volatile-lru -> Evict using approximated LRU among the keys with an expire set.（最近最少使用到的 key 被淘汰）
      # allkeys-lru -> Evict any key using approximated LRU.（最近最少使用到的 key 被淘汰）
      # volatile-lfu -> Evict using approximated LFU among the keys with an expire set.（最少使用到的 key 被淘汰）
      # allkeys-lfu -> Evict any key using approximated LFU.（最少使用到的 key 被淘汰，推荐）
      # volatile-random -> Remove a random key among the ones with an expire set.（随机的 key 被淘汰）
      # allkeys-random -> Remove a random key, any key.（随机的 key 被淘汰）
      # volatile-ttl -> Remove the key with the nearest expire time (minor TTL)（最接近过期时间的 key 被淘汰）
      # noeviction -> Don't evict anything, just return an error on write operations.（不淘汰，返回错误，不推荐）
   ```
   
## 五、redis 集群

### 1. 背景

* 主从复制以及哨兵，他们可以提高读的并发，但是单个 master 容量有限，数据达到一定程度会有瓶颈，这个时候可以通过水平扩展为多 master 集群。  
* redis-cluster：他可以支撑多个 master-slave，支持海量数据，实现高可用与高并发。  
* 哨兵模式其实也是一种集群，他能够提高读请求的并发，但是容错方面可能会有一些问题，比如 master 同步数据给 slave 的时候，这其实是异步复制吧，这个时候 master 宕机了，那么 slave上 的数据就没有 master 新，数据同步需要时间的，1-2 秒的数据会丢失。master 恢复并转换成 slave 后，新数据则丢失。

### 2. 特点

* 每个节点知道彼此之间的关系，也会知道自己的角色，当然他们也会知道自己存在与一个集群环境中，他们彼此之间可以交互和通信。那么这些关系都会保存到某个配置文件中，每个节点都有，这个我们在搭建的时候会做配置的。 
* 客户端要和集群建立连接的话，只需要和其中一个建立关系就行。 
* 某个节点挂了，也是通过超过半数的节点来进行的检测，客观下线后主从切换，和我们之前在哨兵模式中提到的是一个道理。 
* Redis 中存在很多的插槽，又可以称之为槽节点，用于存储数据，这个先不管，后面再说。

### 3. 集群容错

* 构建 Redis 集群，需要至少 3 个节点作为 master，以此组成一个高可用的集群，此外每个 master 都需要配备一个 slave，所以整个集群需要 6 个节点，这也是最经典的 redis 集群，也可以称之为三主三从，容错性更佳。所以在搭建的时候需要有 6 台虚拟机。请各自准备 6 台虚拟机，可以通过克隆去构建，使用单实例的Redis 去克隆即可  
* 集群也可以在单服务器构建，称之为伪集群，但是生产环境肯定是真的，所以建议用 6 台。
* 克隆后务必关闭Redis。

### 4. 构建 Redis 集群

  * redis.conf 配置
    ```shell
    # 开启集群模式
    cluster-enabled yes
    # 每一个节点需要有一个配置文件，需要 6 份。每个节点处于集群的角色都需要告知其他所有节点，彼此知道，这个文件用于存储集
    cluster-config-file nodes-201.conf
    # 超时时间，超时则认为 master 宕机，随后主备切换
    cluster-node-timeout 5000
    # 开启 AOF
    appendonly yes
    ```
  
  * 启动 6 个 redis 实例
    - 启动 6 台 
    - 如果启动过程出错，把 rdb 等文件删除清空

  * 创建集群
    ```shell
    #####
    # 注意1：如果你使用的是 redis3.x 版本，需要使用 redis-trib.rb 来构建集群，最新版使用 C 语言来构建了，这个要注意
    # 注意2：以下为新版的 redis 构建方式
    #####
    # 创建集群，主节点和从节点比例为1，1-3 为主，4-6 为从，1 和 4，2 和 5，3 和 6 分别对应为主从关系，这也是最经典用的最多的集群模式
    redis-cli --cluster create ip1:port1 ip2:port2 ip3:port3 ip4:port4 ip5:port5 ip6:port6 --cluster-replicas 1
    ```
    slots：槽，用于装数据，主节点有，从节点没有

  * 检查集群信息
    ```shell
       redis-cli --cluster check 192.168.25.64:6380
    ```
  * SpringBoot 集成 redis 集群
    ```yaml
       spring:
        redis:
          password: 123456
          cluster: 
            nodes: ip1:port1 ip2:port2 ip3:port3 ip4:port4 ip5:port5 ip6:port6
    ```
  
## 六、缓存穿透与雪崩

### 1. 缓存穿透

* 定义：查询的 key 在 redis 中不存在，在数据库中也不存在，此时被非法用户攻击，大量请求被打到数据库上，造成宕机，从而影响整个系统，这种现象称之为缓存穿透

* 解决方案一：将空值也放入缓存，比如空字符串、空数组、空对象或空 list

* 解决方案二：布隆过滤器，可以快速的判断一个元素是否在一个集合中

### 2. 雪崩

* 定义：大量的 key 同一时间过期，大量请求直接打到数据库，造成宕机

* 预防：
  - 永不过期
  - 过期时间错开
  - 多缓存结合：redis + memcache ...
  - 采购第三方 redis：阿里云、腾讯云...
  
## 七、批量查询优化

常规做法：
```java
    @GetMapping("/getALot")
    public JSONResult getALot(String... keys) {

        List<String> list = new ArrayList<>();

        for (String key : keys) {
            String value = this.get(key);
            list.add(value);
        }
        return JSONResult.ok(list);
    }
    
    @Autowired
	private StringRedisTemplate redisTemplate;
	
    public String get(String key) {
		return (String)redisTemplate.opsForValue().get(key);
	}
```
### 1. multiGet

```java
    public List<String> mget(List<String> keys) {
		return redisTemplate.opsForValue().multiGet(keys);
	}
```

### 2. pipeline

```java
    public List<Object> batchGet(List<String> keys) {

		List<Object> result = redisTemplate.executePipelined(new RedisCallback<String>() {

			@Override
			public String doInRedis(RedisConnection redisConnection) throws DataAccessException {

				StringRedisConnection stringRedisConnection = (StringRedisConnection) redisConnection;
				for (String key : keys) {
					stringRedisConnection.get(key);
				}
				return null;
			}
		});

		return result;

	}
```


















