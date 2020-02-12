# RabbitMQ

本文以Ubuntu18.06操作系统Docker容器环境为例。

## 简介

RabbitMQ是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用Erlang语言编写的，而聚类和故障转移是构建在开放电信平台框架上的。所有主要的编程语言均有与代理接口通讯的客户端库。

## 安装

1. 创建配置文件**rabbitmq.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"

    services:
      # RabbitMQ服务
      server:
        image: rabbitmq:management
        ports:
          - 15672:15672
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
4. 等待部署结束，访问`http://docker-host:15672`。