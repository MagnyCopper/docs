# Redis

> Redis 是一个使用 ANSI C 编写的开源、支持网络、基于内存、分布式、可选持久性的键值对存储数据库。

## 名词解释

- **名词 A**：名词解释；

## 安装说明

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

## 知识点

### Redis 集群

- Redis Sentinal 着眼于高可用，在 master 宕机时会自动将 slave 提升为 master，继续提供服务。
- Redis Cluster 着眼于扩展性，在单个 redis 内存不足时，使用 Cluster 进行分片存储。

### Redis 的数据类型

- string（字符串）
- hash（哈希）
- list（列表）
- set（集合）
- zset(sorted set：有序集合)

## 常见问题

### 大量 key 在同一时间过期有何风险?如何避免?

Redis 可能会出现卡顿,同时会导致大量请求打到 DB 上,引发缓存雪崩;

### 缓存雪崩,穿透,击穿之间是什么区别?

- 缓存雪崩:缓存中有大量的数据在一段时间内全部过期,导致大量请求被数据库处理;
- 缓存穿透:访问了缓存和数据库都没有的数据,导致大量请求被数据库处理;
- 缓存击穿:数据库中存在但是缓存中不存在,导致大量请求被数据库处理;

### 缓存雪崩,穿透,击穿如何处理?

- 缓存雪崩:缓存过期时间添加随机值;
- 缓存穿透:异常数据写入缓存/业务层级添加过滤逻辑(布隆过滤器等);
- 缓存击穿:部分 key 可以考虑设置永不过期/缓存为 null 时设置互斥锁,当数据查询后写入缓存时释放,保证仅有一个线程查询 DB 更新缓存;
