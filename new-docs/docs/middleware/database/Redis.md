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
    - [Cache Aside 旁路缓存](#cache-aside-旁路缓存)
    - [Read/Write Through 读穿/写穿策略](#readwrite-through-读穿写穿策略)
    - [Write Back 写回策略](#write-back-写回策略)
  - [基于Redis的分布式锁](#基于redis的分布式锁)

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

在不考虑分布式锁的场景下,无法保证强一致性,只能保证尽量数据一致

#### Cache Aside 旁路缓存

旁路缓存的方式主要适用于读多写少的场景;

写策略

1. 更新数据库;
2. 删除缓存/更新缓存并添加过期时间或考虑添加分布式锁防止出现数据不一致的问题;

读策略

1. 缓存未命中;
2. 读取库中数据;
3. 回写缓存

尽量采用**先更新数据库后更新缓存**的方式进行处理,由于缓存的更新速度较快,实际环境下数据不一致的概率要比先更新缓存再更新数据库低

#### Read/Write Through 读穿/写穿策略

应用程序只和缓存交互，不再和数据库交互，而是由缓存和数据库交互，相当于更新数据库的操作由缓存自己代理了。

读策略:

1. 先查询缓存判断是否存在;
2. 若不存在则有缓存组件完成查询数据库并更新缓存后返回结果;
3. 若存在直接返回结果;

写策略:

1. 先查询缓存判断是否存在;
2. 若存在则先更新缓存后直接返回,更新数据将由缓存组件更新至数据库
3. 若不存在则先插入缓存后直接返回,新数据将由缓存组件更新至数据库

#### Write Back 写回策略

Write Back（写回）策略在更新数据的时候，只更新缓存，同时将缓存数据设置为脏的，然后立马返回，并不会更新数据库。对于数据库的更新，会通过批量异步更新的方式进行。

一般来说写回策略主要应用在写多读少的场景,CPU缓存/操作系统中应用较多;

写回策略存在数据不是强一致性的，而且会有数据丢失的风险的问题

### 基于Redis的分布式锁

基于redis实现的分布式锁整体来说比较简单,主要就是以下命令

**加锁**:

```bash
# lock_name 即锁的名称
# my_random_value 是由客户端生成的一个随机字符串，它要保证在足够长的一段时间内，且在所有客户端的所有获取锁的请求中都是唯一的，用于唯一标识锁的持有者。
# NX 表示只有当 lock_name(key) 不存在的时候才能 SET 成功
# PX 30000 表示这个锁节点有一个 30 秒的自动过期时间
SET lock_name my_random_value NX PX 30000
```

**解锁**:

```bash
# 判断锁是否存在并校验my_random_value与自己加锁时是否一致
GET lock_name

# 直接删除锁
DEL lock_name
```

但是上述的加锁方式存在以下问题:

- 业务方持有锁的时间超出锁的自动过期时间需要锁续期;
- 解锁时的GET语句与DEL语句不是原子性的,有可能导致解锁失败;
- redis节点故障时的主从一致性问题

**锁续期问题**:

工作线程再获取到redis锁的同时启动一个watchdog监控线程,定期检查工作线程是否存活且继续工作并完成续期工作,续期前一定要检查锁的持有人是否为当前线程;

日常开发中可以考虑采用redisson框架实现,参考代码如下:

```java
RLock lock = redisson.getLock("client-lock"); 
lock.lock(); 
try { 
    //处理业务 
} catch (Exception e) { 
    //处理异常 
} finally { 
    lock.unlock(); 
}
```

**删除锁时的原子性问题**:

借由redis支持使用lua脚本的方式,在脚本中同时完成GET和DEL语句,来保证原子性;

**redis节点故障的主从一致性问题**

使用RedLock方案,但RedLock由于依赖了自动过期时间,导致该方案会依赖不同redis实例的本地时间,由于时间不完全一致可能会导致部分redis实例上的锁提前释放;

**加锁:**

1. 获取当前时间戳;
2. 依次尝试从N个Redis实例中获取锁并设置超时时间,直到每个实例都尝试获取一次;
3. 若存在一半以上的实例获取锁成功,则说明加锁成功(锁的可用时间为过期时间-步骤二的加锁时间);

**解锁:**

1. 对所有redis实例发起删除锁的命令即可;

日常开发中可以考虑采用redisson框架实现,参考代码如下;

```java
Config config1 = new Config();
config1.useSingleServer().setAddress("redis://localhost:5378")
        .setPassword("").setDatabase(0);
RedissonClient redissonClient1 = Redisson.create(config1);
 
Config config2 = new Config();
config2.useSingleServer().setAddress("redis://localhost:5379")
        .setPassword("").setDatabase(0);
RedissonClient redissonClient2 = Redisson.create(config2);
 
Config config3 = new Config();
config3.useSingleServer().setAddress("redis://localhost:5380")
        .setPassword("").setDatabase(0);
RedissonClient redissonClient3 = Redisson.create(config3);
 
String lockKey = "REDLOCK";
RLock lock1 = redissonClient1.getLock(lockKey);
RLock lock2 = redissonClient2.getLock(lockKey);
RLock lock3 = redissonClient3.getLock(lockKey);


RedissonRedLock redLock = new RedissonRedLock(lock1, lock2, lock3);
try {
    if (redLock.tryLock(10, 5, TimeUnit.SECONDS)) {
        //TODO if get lock success, do something;
    }
 }catch(Exception e){
    
 }
```
