# Redis <!-- omit in toc -->

> Redis（Remote Dictionary Server）是一个使用ANSI C编写的开源、支持网络、基于内存、分布式、可选持久性的键值对存储数据库;

目录:

- [简单介绍](#简单介绍)
  - [如何安装?](#如何安装)
  - [常见命令](#常见命令)
    - [基础命令](#基础命令)
    - [List相关命令](#list相关命令)
    - [Set相关命令](#set相关命令)
    - [ZSet相关命令](#zset相关命令)
    - [Hash相关命令](#hash相关命令)
    - [批处理](#批处理)
- [基础属性](#基础属性)
- [其他知识点](#其他知识点)
  - [redis的持久化](#redis的持久化)
  - [AOF日志的重写机制](#aof日志的重写机制)
  - [哨兵集群和主从集群以及Cluster集群的区别?](#哨兵集群和主从集群以及cluster集群的区别)
  - [Redis的key过期策略有哪些?](#redis的key过期策略有哪些)
  - [Redis的内存淘汰策略有哪些?](#redis的内存淘汰策略有哪些)
  - [redis的内存淘汰算法比较](#redis的内存淘汰算法比较)
  - [Redis的key过期策略和内存淘汰策略有什么区别?](#redis的key过期策略和内存淘汰策略有什么区别)
  - [Redis的缓存穿透、缓存击穿和缓存雪崩问题如何解决](#redis的缓存穿透缓存击穿和缓存雪崩问题如何解决)
  - [Redis与DB的数据一致性问题如何解决?](#redis与db的数据一致性问题如何解决)

## 简单介绍

Redis是一个基于内存模式的数据库,主要用来保存键值对形式的数据,同时也支持多种数据类型;

由于传统关系型数据库如MySQL等频繁的涉及磁盘IO操作,而Redis基于内存的特性使其经常作为传统关系型数据的缓存而存在;

### 如何安装?

1. 编辑配置文件`redis.yaml`,并填写以下内容:

   ```yaml
   version: "3.7"

   services:
     # Redis服务
     server:
       image: redis:latest
       ports:
         - 6379:6379
       volumes:
         - server_data:/data
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first

   volumes:
     server_data:
   ```

2. 执行命令`sudo docker stack deploy --compose-file redis.yml redis`；

### 常见命令

#### 基础命令

|   命令   | 说明                             | 说明                 |
| :------: | :------------------------------- | :------------------- |
|   SET    | 向数据库中添加key-value          | `SET key value`      |
|   GET    | 从数据据库中根据key查询对应value | `GET key`            |
|   DEL    | 从数据据库中删除key和对应value   | `DEL key`            |
|  EXISTS  | 判断数据库中key是否存在          | `EXISTS key`         |
|   KEYS   | 列出符合匹配规则的全部key        | `KEYS *me`           |
| FLUSHALL | 删除数据库中全部key              | `FLUSHALL`           |
|   TTL    | 查询key的过期时间                | `TTL key`            |
|  EXPIRE  | 设置key的过期时间                | `EXPIRE key 10`      |
|  SETEX   | 设置key-value的过期时间          | `SETEX key 10 value` |
|  SETNX   | 只有当key不存在时才设置value     | `SETNX key value`    |

#### List相关命令

| 命令  | 说明                   | 说明               |
| :---: | :--------------------- | :----------------- |
| LPUSH | 从左侧向list中插入元素 | `LPUSH key value1` |
| LPOP  | 从左侧向list中删除元素 | `LPOP key`         |
| LLEN  | 查询list的长度         | `LLEN key`         |

#### Set相关命令

|   命令    | 说明                    | 说明                   |
| :-------: | :---------------------- | :--------------------- |
|   SADD    | 向set中添加元素         | `SADD key value1 ...`  |
| SMEMBERS  | 查看set中的元素         | `SMEMBERS key`         |
| SISMEMBER | 查看元素是否已经在set中 | `SISMEMBER key value1` |
|   SREM    | 从set中删除元素         | `SREM key value1`      |

#### ZSet相关命令

|  命令  | 说明                                 | 说明                         |
| :----: | :----------------------------------- | :--------------------------- |
|  ZADD  | 向ZSet中添加元素                     | `ZADD key score1 value1 ...` |
| ZSCORE | 查看ZSset中的元素的分数              | `ZSCORE key value1`          |
| ZRANK  | 按score从小到大的顺序查看value的排名 | `ZRANK key value1`           |
|  ZREM  | 从zset中删除元素                     | `ZREM key value1`            |

#### Hash相关命令

|  命令   | 说明                    | 说明                        |
| :-----: | :---------------------- | :-------------------------- |
|  HSET   | 向map中添加键值对       | `HSET name key1 value1 ...` |
|  HGET   | 从map中根据键获取值     | `HGET name key1`            |
| HGETALL | 查询该map下的全部键值对 | `HGETALL name`              |
|  HDEL   | 从map中删除键值对       | `HDEL name key1`            |
| HEXISTS | 判断该键是否保存在map中 | `HEXISTS name key1`         |

#### 批处理

redis是支持命令批处理的,参考以下代码

```redis
MULIT
ADD name key1
LPUSH name1 a b c
EXEC/DISCARD
```

MULIT和EXEC/DISCARD之间的命令将被按顺序依次执行,即使中间命令出错也会继续执行后面的命令;

redis时不完整支持事务的,无法做到出错后的数据回滚;

## 基础属性

- 数据类型:String,List,Set,SortedSet(ZSet),Hash,Stream,Geospatial,HyperLogLog,Bitmap,Bitfield;
- redis的key是需要区分大小写的;
- redis中保存键值对均是以二进制的形式保存的;

## 其他知识点

### redis的持久化

redis的数据持久化主要有2个方式:

| 名称  | 说明                          | 优缺点                                                                                                                                                                  |
| :---: | :---------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|  RDB  | 即快照,定时完整备份数据到本地 | 1.会丢失从快照备份后到事故之间的全部数据<br> 2.写入磁盘时是阻塞状态会影响其他服务(可以通过BGSAVE的方式通过fork子线程的方式进行异步快照但依然存在fork子线程时的阻塞现象) |
|  AOF  | 全部操作的日志式记录          | 1.恢复速度相较RDB方式较慢<br> 2.日志文件体积较大占用磁盘空间;                                                                                                           |
| 混合  | RDB+AOF                       | 结合上述2种方式的优点                                                                                                                                                   |

### AOF日志的重写机制

由于AOF模式的持久化是通过类似binlog的方式记录每次redis操作,因此aof文件将会随着时间越来越大,为了解决这个问题redis引入了AOF的重写机制;

主要流程如下:

1. 当AOF文件膨胀到临界值时,redis将fork一个子线程处理重写流程;
2. 子线程将fork时redis内存中的数据直接以set值得方式记录到新AOF文件中;
3. 为了防止丢失重写过程中更新的数据,重写时主线程接收的任何更新操作都将同步写到更新缓存中;
4. 当重写子线程完成redis内存数据保存后,会将操作缓存的数据追加写道AOF文件中;
5. 全部操作缓存落盘完毕后,通知主线程重写结束;

### 哨兵集群和主从集群以及Cluster集群的区别?

|  集群类别   | 说明                                                                                                    |
| :---------: | :------------------------------------------------------------------------------------------------------ |
|  主从集群   | 即读写分离,主节点负责写入数据并将数据异步同步给从节点,读取操作可以从主/从节点进行读取,提升性能          |
|  哨兵集群   | 以独立进程的方式监控主从集群的主节点的存活状态,一旦宕机将由选举从从节点中产生的新节点作为主节点提供服务 |
| Cluster集群 | 将数据分片后保存在不同的节点上,可以支持超大内存需求的存储                                               |

### Redis的key过期策略有哪些?

redis中主要采用以下2种key过期策略:

- 惰性删除:访问key时顺便检查该key的过期时间,若已过期则直接返回NULL;
- 定时删除:每间隔一定时间(默认为100ms)redis将随机从过期字典种挑选一定数量(默认是20个)key进行过期检查,若过期则删除;

redis种同时采用上述2种过期删除策略,在效率与时间之间达成一定平衡;

### Redis的内存淘汰策略有哪些?

当redis的内存空间不足且有新的key需要写入时,将出发内存淘汰策略

redis的内存淘汰策略主要有以下8个

|    淘汰策略     | 作用范围 | 算法   | 说明                                                    |
| :-------------: | :------- | :----- | :------------------------------------------------------ |
|   noeviction    | -        | -      | 不处理直接报错                                          |
|   allkeys-lru   | allkeys  | lru    | 在键空间中，移除最近最少使用的key                       |
|   allkeys-lfu   | allkeys  | lfu    | 在键空间中，移除使用频率最少的key                       |
| allkeys-random  | allkeys  | random | 在键空间中，随机移除某个key                             |
|  volatile-lru   | volatile | lru    | 在设置了过期时间的键空间中，移除最近最少使用的key       |
|  volatile-lfu   | volatile | lfu    | 在设置了过期时间的键空间中，移除使用频率最少的key       |
| volatile-random | volatile | random | 在设置了过期时间的键空间中，随机移除某个key             |
|  volatile-ttl   | volatile | ttl    | 在设置了过期时间的键空间中，有更早过期时间的key优先移除 |

### redis的内存淘汰算法比较

redis将在内存不足时从作用范围的key空间中选取N个key按照以下算法进行淘汰

- LRU:最近没有使用的某个key
- LFU: 最近访问频率低的某个key
- TTL: 过期时间最早的某个key
- RANDOM: 随机删除某个key

### Redis的key过期策略和内存淘汰策略有什么区别?

|    类别     | 说明                                       |
| :---------: | :----------------------------------------- |
| key过期策略 | key过期策略重点在key的过期时间及删除方式上 |
|  内存淘汰   | 内存淘汰重点在redis内存空间的释放          |

### Redis的缓存穿透、缓存击穿和缓存雪崩问题如何解决

|   类别   | 现象                                                                   | 解决办法                                 |
| :------: | :--------------------------------------------------------------------- | :--------------------------------------- |
| 缓存穿透 | 某个热点 key 的突然过期                                                | 添加限流和服务降级                       |
| 缓存击穿 | redis和 DB 中均没有的查询结果,如:自增序列的 ID 但是不断使用-1 调用接口 | 请求参数添加校验/布隆过滤器/将无效值缓存 |
| 缓存雪崩 | 大量缓存集中过期                                                       | 过期时间添加随机值                       |

### Redis与DB的数据一致性问题如何解决?