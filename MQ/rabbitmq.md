# RabbitMQ

> RabbitMQ是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用Erlang语言编写的，而聚类和故障转移是构建在开放电信平台框架上的。所有主要的编程语言均有与代理接口通讯的客户端库。

## 名词解释

- **AMQP**：高级消息队列协议即Advanced Message Queuing Protocol（AMQP）是面向消息中间件提供的开放的应用层协议，其设计目标是对于消息的排序、路由（包括点对点和订阅-发布）、保持可靠性、保证安全性。AMQP规范了消息传递方和接收方的行为，以使消息在不同的提供商之间实现互操作性，就像SMTP，HTTP，FTP等协议可以创建交互系统一样。与先前的中间件标准（如Java消息服务）不同的是，JMS在特定的API接口层面和实现行为上进行了统一，而高级消息队列协议则关注于各种消息如何以字节流的形式进行传递。因此，使用了符合协议实现的任意应用程序之间可以保持对消息的创建、传递；
- **交换机(Exchange)**：作为生产者和消息队列之间得抽象层，生产正将消息发送给交换机并根据bind和路由键进行匹配发送至对应得队列中，如果没有Queue bind到Exchange的话，它会直接丢弃掉Producer发送过来的消息。交换机具有4种类型：Direct,、topic、 Headers、Fanout；

  - **Direct**：direct：是”先匹配, 再投送”. 即在绑定时设定一个 routing_key, 消息的routing_key 匹配时, 才会被交换器投送到绑定的队列中去.
  - **Topic**：按规则转发消息（最灵活）
  - **Headers**：设置 header attribute 参数类型的交换机
  - **Fanout**：转发消息到所有绑定队列

## 安装说明

1. 创建配置文件**rabbitmq.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"

    services:
      # RabbitMQ服务
      server:
        image: rabbitmq:management
        ports:
          # 管理端口
          - 15672:15672
          # AMQP协议端口
          - 5672:5672
        volumes:
          - server_data:/var/lib/rabbitmq
        environment:
          RABBITMQ_DEFAULT_USER: root
          RABBITMQ_DEFAULT_PASS: tszx11hcm
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first

    volumes:
      server_data:
    ```
3. 执行部署命令`sudo docker stack deploy -c=rabbitmq.yml rabbitmq`;
4. 等待部署结束，访问`http://docker-host:15672`登录管理界面。