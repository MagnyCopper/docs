# Apache Kafka

> Kafka 是由 Apache 软件基金会开发的一个开源流处理平台

## 概念解释

- **概念 A**：概念解释；

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
         - KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS=server:9092
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
