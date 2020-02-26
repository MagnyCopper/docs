# RabbitMQ

本文以Ubuntu18.06操作系统Docker容器环境为例。

## 安装

### Docker部署

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