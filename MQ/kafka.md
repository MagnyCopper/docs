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
6. 启动KAFKA`bin/kafka-server-start.sh config/server.properties`,后台运行命令`bin/kafka-server-start.sh config/server.properties 1>/dev/null 2>&1 &`若看到`INFO [KafkaServer id=0] started (kafka.server.KafkaServer)`字样说明KAFKA已成功启动;
7. 从官网下载CMAK的安装包`wget https://github.com/yahoo/CMAK/releases/download/3.0.0.4/cmak-3.0.0.4.zip`,当前的最新版本为3.0.0.4;
8. 解压缩下载安装包`unzip cmak-3.0.0.4.zip`并进入解压缩目录;
9. 修改配置中的Zookeeper地址`vim conf/application.conf`
    ```conf
    cmak.zkhosts="192.168.50.100:2181"
    ```
10. 执行`bin/cmak`启动CMAK,从浏览器访问`http://192.1168.50.52:9000`进入WebUI界面,并添加KAFKA集群即可;