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
  - [哨兵集群和主从集群的区别?](#哨兵集群和主从集群的区别)
  - [Redis的key过期策略有哪些?](#redis的key过期策略有哪些)

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
|  AOF  | 全部操作的日志式记录          | 1.恢复速度相较RDB方式较慢                                                                                                                                               |

### 哨兵集群和主从集群的区别?

### Redis的key过期策略有哪些?
