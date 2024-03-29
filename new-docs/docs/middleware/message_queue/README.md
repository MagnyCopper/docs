# 消息队列

> 消息队列，是基础数据结构中“先进先出”的一种数据结构。一般用来解决应用解耦，异步消息，流量削峰等问题，实现高性能，高可用，可伸缩和最终一致性架构。

## 主目录

- [Kafka](./Kafka.md)

## 知识点

### 消息队列的特点

- **解耦**：产生一条数据发到 MQ 里，谁需要谁去 MQ 里消费即可，不需要一个个考虑给谁发送；
- **异步**：系统 a 发送到 MQ 就直接返回响应了，而不必等其他系统都执行完才响应，其他系统从 MQ 取然后本地执行即可；
- **削峰**：高峰期的大量请求，通过 MQ 消化掉，系统从 MQ 中取自己能处理的数量即可，MQ 中其他请求在会慢慢分配到低峰期。

### 各个消息中间件的对比

| 中间件名称 | 优点                                                     | 缺点                         |
| :--------: | :------------------------------------------------------- | :--------------------------- |
|   Kafka    | 1.性能优异;<br>2.社区活跃;                               | 1.延时较高(消息不是立即发送) |
|  RabbitMQ  | 1.支持 Exchange 路由功能;                                | 1.消息积压时性能严重下降     |
|  RocketMQ  | 1.与 Java 环境较为兼容;<br>2.消息延时较低;<br>3.性能优异 | 1.社区不活跃                 |

### 如何保证高可用

| 中间件名称 | 高可用实现方式                                        |
| :--------: | :---------------------------------------------------- |
|   Kafka    | 队列支持分区,每个分区的数据级副本分散于多个 broker 上 |
|  RabbitMQ  | 集群镜像模式,每个节点保存相同的数据                   |
|  RocketMQ  | 同时支持 kafka 和 rabbitmq 的高可用模式               |

### 如何保证消息不重复

- 计算消息特征值,并将其保存至缓存/数据库中,每次消费前检查该特征值是否已经被消费;
- 消息队列的幂等配置(如 Kafka 的消息幂等);

### 如何保证消息不丢失

- **生产者**:采用最严格的 ack 和重试机制,确保消息送达中间件;
- **中间件**:采用及时写入磁盘的方式防止由于故障停机导致的消息丢失;
- **消费者**:采用关闭最自动提交的方式由程序确认完成数据消费后再手动提交;

### 如何保证消费顺序

- **rabbitMQ**:单 queue 对应一个消费者即可保证顺序消费;
- **kafka**:单 partition,消费者单线程处理即可保证顺序消费;
