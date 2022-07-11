# Apache Kafka

> Kafka 是由 Apache 软件基金会开发的一个开源流处理平台

## 概念解释

- **topic**：发布订阅对象就是 topic；
- **producer/consumer**：向 topic 发布 message 的客户端程序称为 producer,从 topic 订阅消费消息的客户端称为 consumer；
- **broker**：一个集群由多个 broker 组成,broker 负责接收和处理客户端发送过来的请求,并完成对 message 的持久化,同一个服务器上可以运行多个 broker 进程；
- **replicas**：即将相同的数据拷贝到多个 broker 上(多台机器),分为两类:leader 和 follower；
- **partition**：即将每个 topic 划分为多个 partition,每个 partition 是一组有序的 message,producer 生产的 message 仅会发往一个 partition,每个 partition 可以建立若干 replicas,其中只能有一个 leader 和 N-1 个 follower；
- **consumer group**：多个 consumer 实例共同构成一个 consumer group,用来消费多个 topic,这一组 topic 的每个分区仅会被组内的一个 consumer 消费；
- **coordinator**：coordinator专门为consumer group 服务,主要负责执行rebalance,offset管理,group成员管理等；
- **rebalance**：consumer group中某个consumer实例挂掉后,其他consumer自动重新分配订阅topic的partition的过程；
- **AR(Assigned Replicas)**：分区中所有的replicas的统称分为OSR和ISR；
- **OSR(Out Sync Replicas)**：follower与leader差距较大,没有完全同步或滞后的副本合集；
- **ISR（In Sync Replicas）**：与leader保持一致的副本合集,如果某个在ISR中的副本落后太多将会被自动移除ISR,某个OSR中副本完成同步则会移动至ISR,当leader副本发生故障时,只有在ISR中的follower副本有机会选举为新的leader(也可以通过设置unclane.enable实现OSR副本选举)；
- **HW（High Watermark）**：consumer只能消费到HW之前的消息,HW由ISR副本中最老的offset决定；

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

## 注意事项/其他

1. xxxxxxx；
