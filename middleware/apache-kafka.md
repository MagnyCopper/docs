# Apache Kafka

> Kafka 是由 Apache 软件基金会开发的一个开源流处理平台

## 概念解释

- **topic**：发布订阅对象就是 topic；
- **producer/consumer**：向 topic 发布 message 的客户端程序称为 producer,从 topic 订阅消费消息的客户端称为 consumer；
- **broker**：一个集群由多个 broker 组成,broker 负责接收和处理客户端发送过来的请求,并完成对 message 的持久化,同一个服务器上可以运行多个 broker 进程；
- **replicas**：即将相同的数据拷贝到多个 broker 上(多台机器),分为两类:leader 和 follower；
- **partition**：即将每个 topic 划分为多个 partition,每个 partition 是一组有序的 message,producer 生产的 message 仅会发往一个 partition,每个 partition 可以建立若干 replicas,其中只能有一个 leader 和 N-1 个 follower；
- **consumer group**：多个 consumer 实例共同构成一个 consumer group,用来消费多个 topic,这一组 topic 的每个分区仅会被组内的一个 consumer 消费；
- **coordinator**：coordinator 专门为 consumer group 服务,主要负责执行 rebalance,offset 管理,group 成员管理等；
- **rebalance**：consumer group 中某个 consumer 实例挂掉后,其他 consumer 自动重新分配订阅 topic 的 partition 的过程；
- **AR(Assigned Replicas)**：分区中所有的 replicas 的统称分为 OSR 和 ISR；
- **OSR(Out Sync Replicas)**：follower 与 leader 差距较大,没有完全同步或滞后的副本合集；
- **ISR（In Sync Replicas）**：与 leader 保持一致的副本合集,如果某个在 ISR 中的副本落后太多将会被自动移除 ISR,某个 OSR 中副本完成同步则会移动至 ISR,当 leader 副本发生故障时,只有在 ISR 中的 follower 副本有机会选举为新的 leader(也可以通过设置 unclane.enable 实现 OSR 副本选举)；
- **HW（High Watermark）**：consumer 只能消费到 HW 之前的消息,HW 由 ISR 副本中最老的 offset 决定；

## 安装说明

### 相关依赖

- Apache ZooKeeper

### 安装 Kafka

1. 创建配置文件`kafka.yaml`；
2. 在配置文件中填写以下内容:

   ```yaml
   version: "3.7"

   services:
     # Apache Kafka服务
     server:
       image: bitnami/kafka:latest
       ports:
         - 9092:9092
       volumes:
         - server_data:/bitnami/kafka
       environment:
         - KAFKA_BROKER_ID=1
         - KAFKA_CFG_ZOOKEEPER_CONNECT=192.168.10.5:2181
         - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092
         - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://192.168.10.5:9092
         - ALLOW_PLAINTEXT_LISTENER=yes
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first
     # kafka ui服务
     ui:
       image: provectuslabs/kafka-ui:latest
       ports:
         - 8080:8080
       environment:
         - KAFKA_CLUSTERS_0_NAME=kafka
         - KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=192.168.10.5:9092
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first
       depends_on:
         - server
   volumes:
     server_data:
   ```

3. 执行命令`sudo docker stack deploy --compose-file kafka.yml kafka`；

## 其他

### **kafka 为啥性能好?**

1. 分区机制;
2. 支持数据压缩;
3. 支持批量发送和读取;
4. MMAP 技术(Memory Mapped Files 内存映射文件);
5. 零拷贝(linux 的 Sendfile 方法);
6. 顺序读写;

### **message 将发送到哪个 partition?**

有以下几种策略:

1. 轮询,即依次将 message 发送到各个 partition;
2. 随机,随机将 message 发送到各个 partition;
3. 按 message 的 key 决定发送到哪个 partition(计算 key 的 Hash 值)

kafka 默认采用 2 种策略:若指定了 message 的 key 则按照 key 计算 partition,若没有指定 key 则采用轮询的策略;

### **producer 生产的 messge 如何进行压缩?**

> 当 producer 服务器的 CPU 资源充足时建议开启压缩以节约网络带宽和磁盘空间;

**如何压缩?**

producer 生产消息时,若指明压缩算法即可完成对消息的压缩发送;

broker 服务端设置压缩后,即可实现 message 的压缩保存;

**存在 2 种意外情况可能导致 broker 的额外压缩:**

1.broker 和 producer 指明了不同压缩算法;

2.kafka 消息格式发生转换(v1->v2);

**如何解压缩?**

消息发送到 broker 后由 broker 原样保存至本地文件,当有 consumer 消费该消息时,将有 consumer 程序在本地完成消息解压;

### **各个 partition 将被哪个 consumer 消费?**

有以下几种策略:

1. 轮询 Round,默认值,即依次将各个分区指派给对应的 consumer 消费;
2. Range,即采用 partition 数/consumer 数,然后按顺序指派给 consumer,余数部分分配给第一个 consumer;
3. Sticky,Range 的强化版,当有新的 consumer 加入或退出时,将不会按照 range 重新分配而是保留当前的消费策略,将空余的分区平均分给其他 consumer(新增同理);

### **如何进行 rebalance?**

> rebalance 时所有的 consumer 是暂停消费的!

**触发条件:**

1. group 内的 consumer 数量发生变化(心跳丢失等原因);
2. 若采用模糊匹配方式订阅 topic 时,新增或减少 topic;
3. 订阅的 topic 中 partition 数量发生变化;

### **Replicas 机制?**

replicas 分为 2 类即:leader 和 follower

客户端发送和接受 message 都是通过 leader 来完成的;

follower 副本的唯一任务就是异步拉取 leader 的 message 进行同步;

**ISR 机制**

ISR 中的副本都是与 leader 最为接近的副本即同步的,不在其中的被认为是不同步的;

ISR 是一个动态的集合,leader 副本本身也在其中,只有副本与 leader 的差距时间小于配置值(replica.lag.time.max.ms)才会被算进 ISR;

**副本选举**

一般来说副本选举的范围实在 ISR 中的,当然也可以通过配置的方式实现从 OSR 中进行 unclean 选举(可能存在丢失消息的风险);

由于仅用 leader 对外提供服务,因此当 leader 副本不可用时意味着整个分区不可用,需要进行重新选举;

### **message 机制?**

**幂等**

producer 在生产消息时可以通过参数指定消息的幂等属性,broker 将通过比较消息对相同消息进行丢弃

producer 的幂等性仅能在单个 partition 上保证;

**事务**

producer 在生产消息时也是支持事务的,即一批消息要不全都写入成功要不全部写入失败;

**防止 message 丢失**

1. 使用带有回调的 send 方法,及时处理异常;
2. 发送时设置 ack=all,即全部副本的 broker 都接受到消息时才算做已提交;
3. 发送时添加重试次数参数,发送失败时自动重试;
4. 发送时仅用 autocommit,使用手动提交 offset 的方式进行控制;
5. broker 端禁用 unclean 选举;
6. broker 端设置消息的副本数及最小副本写入数;

**防止 message 重复**

1. 防止出现拉取消息后未及时完成处理导致的心跳丢失,rebalance 或自动提交;
2. 必要时可以开启 producer 的消息幂等配置;
3. 引入业务字段的 ID 在处理程序中完成去重;
4. 必要时可以考虑设置禁用重试/降低 ACK 要求等

**保证 message 顺序**

1. 单个 partition(发送时指定/设置相同的 key/仅设置 1 个 partition);
