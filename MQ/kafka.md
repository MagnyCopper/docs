# Apache Kafka

Apache Kafka: A Distributed Streaming Platform.

## 安装

本文以Ubuntu18.06操作系统Docker容器环境为例。

### 安装Apache Kafka

#### 二进制安装

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
    # 设置外部访问使用的IP地址
    listeners=PLAINTEXT://192.168.50.100:9092
    ```
6. 启动KAFKA`bin/kafka-server-start.sh config/server.properties`,后台运行命令`bin/kafka-server-start.sh config/server.properties 1>/dev/null 2>&1 &`若看到`INFO [KafkaServer id=0] started (kafka.server.KafkaServer)`字样说明KAFKA已成功启动;

### 安装CMAK（Cluster Manager for Apache Kafka）

#### 二进制安装

1. 从官网下载CMAK的安装包`wget https://github.com/yahoo/CMAK/releases/download/3.0.0.4/cmak-3.0.0.4.zip`,当前的最新版本为3.0.0.4;
2. 解压缩下载安装包`unzip cmak-3.0.0.4.zip`并进入解压缩目录;
3. 修改配置中的Zookeeper地址`vim conf/application.conf`
    ```conf
    cmak.zkhosts="192.168.50.100:2181"
    ```
4. 执行`bin/cmak`启动CMAK，需要后台运行执行`nohup bin/cmak &`，也可以添加`-Dhttp.port=9001`参数修改启动端口；
5. 从浏览器访问`http://192.168.50.52:9000`进入WebUI界面,并添加KAFKA集群即可;

#### docker安装

1. 创建配置文件**kafka-manager.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"

    services:
      # kafka-manager服务
      server:
        image: kafkamanager/kafka-manager
        ports:
          # 管理端口
          - 9000:9000
        environment:
          ZK_HOSTS: 192.168.50.100
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
    ```
3. 执行部署命令`sudo docker stack deploy -c=kafka-manager.yml kafka-manager`;
4. 等待部署结束，访问`http://docker-host:9000`登录管理界面。