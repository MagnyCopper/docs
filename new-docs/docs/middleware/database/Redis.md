# Redis <!-- omit in toc -->

> Redis（Remote Dictionary Server）是一个使用ANSI C编写的开源、支持网络、基于内存、分布式、可选持久性的键值对存储数据库;

目录:

- [简单介绍](#简单介绍)
  - [如何安装?](#如何安装)
  - [常见命令](#常见命令)
- [基础属性](#基础属性)
- [其他知识点](#其他知识点)
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

## 基础属性

- 数据类型:String,List,Set,SortedSet,Hash,Stream,Geospatial,HyperLogLog,Bitmap,Bitfield;
- redis的key是需要区分大小写的;
- redis中保存键值对均是以二进制的形式保存的;

## 其他知识点

### 哨兵集群和主从集群的区别?

### Redis的key过期策略有哪些?
