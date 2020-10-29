# Kafka

## 名词解释

- **Topic**:Topic是用于存储消息的逻辑概念
- **Partition**: 每个topic可以有一个或多个partition（分区）。分区是在物理层面上的，不同的分区对应着不同的数据文件。Kafka使用分区支持物理上的并发写入和读取，从而大大提高了吞吐量
- **Broker**: 消息队列中常用的概念，在Kafka中指部署了Kafka实例的服务器节点
- **CousumerGroup**:一个消费者组可以包含一个或多个消费者。使用多分区+多消费者方式可以极大提高数据下游的处理速度。
- **ISR**:In-Sync Replicas副本同步队列,ISR是由leader维护follower从leader同步数据产生延迟,超过阈值都会把follower剔除出ISR, 存入OSR;
- **OSR**:Outof-Sync Replicas
- **AR**:Assigned Replicas 所有副本,AR=ISR+OSR

## Kafka核心概念

### 消息

消息是Kafka中最基本的数据单元,消息由一串字节构成,其中主要由Key和Value构成,Key和Value也都是byte数组;<br>
**Key**的主要作用是根据唯一的策略,将此消息路由到指定的分区中,Key可以是null;<br>
**Value**的主要作用是作为消息真正的负载,为了提高网络和存储的利用率,生产者会批量的发送消息到kafka,并在发送之前对消息进行压缩;<br>

### Topic&分区&Log

Topic是用于存储消息的逻辑概念,也可以看做一个消息的集合.每个Topic可以用多个生产者向其中推送(push)消息,也可以有任意多个消费者消费其中的消息;<br>
每个Topic可以划分成多个分区(每个Topic至少都有一个分区),同一Topic下的不同分区包含的消息是不同的.每个消息再被添加到分区时,都会被分配一个offset,他是消息在此分区中的唯一编号,Kafka通过offset保证消息在分区内的顺序,Kafka只保证在同一个分区内的消息是有序的,同一topic的不同分区会分配在不同的Broker上;<br>
分区在逻辑上对应一个Log,当生产者将消息写入分区时,实际上是写入到了分区对应的Log中.Log是一个逻辑概念,可以对应到磁盘上的一个文件夹.Log由多个Segment组成,每个Segment对应一个日志文件和索引文件,新消息均续写在文件的结尾.每个日志文件的大小是有限制的,当超出限制后则会创建新的Segment;<br>

### 保留策略&日志压缩

为了避免磁盘被占满,Kafka会配置相应的保留策略,以实现周期性的删除陈旧的消息;<br>
保留策略:Kafka中有2种保留策略,一种是根据消息保留的时间,当消息在Kafka中保存的时间超过了指定的时间,就可以被删除;另一种是根据Topic存储的数据大小,当Topic所占的日志文件大小大于一个阈值,则可以开始删除最旧的消息;<br>
日志压缩:Kafka会启动一个后台线程,定期将相同Key的消息进行合并,只保留最新的value值;<br>

### Broker

一个单独的的Kafka Server 就是一个Broker,Broker的主要工作就是接受生产者发过来的消息,分配offset,之后保存到磁盘中;同时,接受消费者,其他Broker的请求,根据请求类型进行相应的处理并返回;<br>

### 副本

Kafka对消息进行了冗余备份,每个分区可以有多个副本,每个分区至少有一个副本,当分区中只有一个副本时,就只有Leader副本,没有Follower副本;<br>
每个分区的副本集合中,都回选举出一个副本最为Leader副本,Kafka在不同的场景下会采用不同的选举策略;所有的读写请求都由选举出来的Leader副本处理,其他都作为Follower副本,Follower副本仅仅是从Leader副本处吧数据拉取到本地之后,同步更新到自己的Log中;<br>
一般情况下,同一分区下的多个副本被分配到不同的Broker中,这样当leader所处的Broker宕机之后,可以选举新的Leader副本,继续对外提供服务;<br>

### ISR集合

ISR集合表示的是目前"可用"且消息量与leader相差不多的副本集合,成为ISR副本有2个条件:副本所在的节点必须维持着与Zookeeper的链接,副本最后一条消息的offset与Leader副本的最后一条消息的offset之间的差值不能超过指定的阈值,若不满足则被剔除ISR队列,后面满足要求后,重新加入队列;<br>

### HW&LEO

通过说明一个消息被推送到一个分区上的过程,解释HW和LEO(当前副本的最后一个offset)的作用;<br>

1. 生产者向此分区推送消息;
2. leader副本向消息追加到Log中,并递增其LEO;
3. Follower副本从Leader副本拉取消息进行同步;
4. Follower副本将拉取到的消息更新到本地Log中,并递增其LEO;
5. 当ISR集合中所有副本完成对该消息的同步,Leader副本会递增HW;
6. 经过以上流程,该消息就对消费者可见了;

### Cluster&Controller

多个Broker可以作为一个Cluster对外提供服务,每个Cluster当中会选举出一个Broker来担任Controller,Controller是整个Kafka集群的指挥中心;<br>

### 生产者

生产者的主要工作就是生产消息,并将消息按照一定的规则推送到topic的分区中;<br>

### 消费者

消费者的主要工作时从topic中拉取消息,并对消息进行消费,某个消费者消费到分区的哪个位置(offset)的相关信息,是由消费者自己维护的;<br>

### 消费者组

在kafka中,多个消费者可以组成一个消费者组,一个消费者只能属于一个消费者组,消费者组保证其订阅的Topic的每个分区只能被分配给此消费者组中的一个消费者,若要实现一个消息可以被多个消费者同时消费(广播)的效果,则要将每个消费者放入单独的消费者组;<br>
当topic的分区数大于消费者数时,会导致有个别消费者需要消费多个分区上的消息,补充新的消费者到消费者组(或移除消费者)时会触发重平衡动作重新分配分区与消费者的对应关系;<br>

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
6. 启动KAFKA`bin/kafka-server-start.sh config/server.properties`,后台运行命令`bin/kafka-server-start.sh -daemon config/server.properties &`若看到`INFO [KafkaServer id=0] started (kafka.server.KafkaServer)`字样说明KAFKA已成功启动;

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