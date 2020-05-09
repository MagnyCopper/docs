# Apache Kafka

Apache Kafka: A Distributed Streaming Platform.

## 安装

本文以Ubuntu18.06操作系统Docker容器环境为例。

### 二进制部署安装

1. 安装JDK提供Java运行环境`sudo apt install default-jdk`;
2. 创建操作文件夹`mkdir KAFKA`;
3. 从官方网站下载二进制安装包`wget https://apache.website-solution.net/kafka/2.5.0/kafka_2.12-2.5.0.tgz`，当前的最新版本为2.5.0;
4. 解压缩文件`tar -xzf kafka_2.12-2.5.0.tgz`,并进入该文件夹`cd kafka_2.12-2.5.0`;
5. 修改配置配置文件`vim config/server.properties`;
    ```properties
    # broker的id,在集群中是唯一的
    broker.id=0
    # 设置访问的ZooKeeper地址和端口,多个节点使用,分隔开
    zookeeper.connect=192.168.50.100:2181
    ```
6. 启动KAFKA`bin/kafka-server-start.sh config/server.properties`若看到`INFO [KafkaServer id=0] started (kafka.server.KafkaServer)`字样说明KAFKA已成功启动;