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

### redis 的持久化方案

#### RDB 镜像全量

通过创建快照的方式,将某个时间点内存的快照写入本地磁盘中,该快照主要用于主从集群的数据同步,同时也用于 redis 重启时的数据加载;

#### AOF 增量持久化

增量持久化,没执行一条会更改 redis 中数据的命令,redis 便会将写入到内存缓存中,再根据设置来决定何时同步到本地磁盘;

为了兼顾性能和数据,一般选择每秒写入一次 AOF 文件;

#### 混合持久化

redis4.0 开始支持 RDB 和 AOF 的混合持久化;

## 常见问题

### 大量 key 相同时间过期有何风险?

大量 key 过期将导致 redis 的短暂卡顿,严重时会导致缓存雪崩,因此应该在过期时间上加上个随机值,使过期时间分散些;

### 基于 redis 的分布式锁

1. 首先使用 setnx 命令争抢锁;
2. 使用 expire 给锁添加过期时间;
3. 也可以使用带有参数的 setnx 指令实现 setnx 和 expire 命令合并;

### 如何按照模式查找 key

可以使用 keys 命令,但要注意由于 redis 的单线程机制,keys 会阻塞主线程,导致服务中断,这种场合应该使用 scan 命令;

### redis 的消息队列

简单点点对点可以使用 list 数据结构,一边 rpush,一边 lpop(非阻塞)/blpop(阻塞)

一对多环境可以采用 pub/sub,满足生产:消费的 1:N 需求,即一条消息被多个消费者消费;

pub/sub 有个问题在于消费端不在线时会丢失消息,若有此场景应考虑引入专业的消息中间件;

### 缓存的雪崩击穿和穿透

- 缓存雪崩:大量缓存集中过期(过期时间添加随机值);
- 缓存击穿:缓存和 DB 中均没有的查询结果,自增序列的 ID 但是不断使用-1 调用接口(请求参数添加校验/布隆过滤器/将无效值缓存);
- 缓存穿透:某个热点 key 的突然过期(添加限流和服务降级)

### 布隆过滤器

当一个元素被加入集合时，通过 K 个散列函数将这个元素映射成一个位数组中的 K 个点，把它们置为 1。检索时，我们只要看看这些点是不是都是 1 就（大约）知道集合中有没有它了：如果这些点有任何一个 0，则被检元素一定不在；如果都是 1，则被检元素很可能在。

**缺点**:

存在误判,删除困难;

**实现**:

使用时需要指明预估数量和期望的误判率

实现时需要指明 hash 函数和 bit 数组的大小;

推荐使用 guava 中的 BloomFilter;

**场景**:

- 垃圾邮件过滤;
- 爬虫网址过滤;

### Redis 的 key 淘汰策略

key 的过期策略一般是由定期删除+惰性删除组合而来的;

- 定期删除:每过 100s 就随机抽取一些过期的 key 进行删除(不全部删除的原因是全表查询 redis 很消耗资源)
- 惰性删除:当查询时发现 key 过期直接删除;

redis 通过一个过期字典来保存数据的过期时间,过期字典的 key 指向某个 key,value 指向一个对应 key 的过期时间;

若定期删除没命中同时也灭有查询过该 key 怎么删除呢?

- volatile-lru（least recently used）：从已设置过期时间的数据集（server.db[i].expires）中挑选最近少使用的数据淘汰
- volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
- volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
- allkeys-lru（least recently used）：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的 key（这个是最常用的）
- allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
- no-eviction：禁止驱逐数据，也就是说当内存不足以容纳新写入数据时，新写入操作会报错。这个应该没人使用吧！
- volatile-lfu（least frequently used）：从已设置过期时间的数据集（server.db[i].expires）中挑选最不经常使用的数据淘汰
- allkeys-lfu（least frequently used）：当内存不足以容纳新写入数据时，在键空间中，移除最不经常使用的 key

总结如下:

| 方式           | 间隔             |
| -------------- | ---------------- |
| 最近最少使用的 | 已设置过期时间的 |
| 将要过期的     | 已设置过期时间的 |
| 随机           | 已设置过期时间的 |
| 最不经常使用的 | 已设置过期时间的 |
| 最近最少使用的 | 全部 key         |
| 随机           | 全部 key         |
| 最不经常使用的 | 全部 key         |
| 不驱逐         | 不驱逐           |

一个简单的 LRU 实现(容量为 100,超过时自动删掉最老的)

```java
Map<Long, Object> cacheLRU = Collections.synchronizedMap(new LinkedHashMap<Long, Object>(100, .75f, true) {
    @Override
    protected boolean removeEldestEntry(Map.Entry<Long, Object> eldest) {
        return size() > 100;
    }
});
```

### redis 是否支持事务如何操作?

redis 是支持事务的,但是不支持事务失败的回滚操作;可以简单的理解为将多条指令打包处理

启用事务的方法如下:

```bash
MULTI
...
EXEC
```
