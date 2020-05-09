# ZooKeeper

本文以Ubuntu18.06操作系统Docker容器环境为例。

## 安装

### Docker部署

1. 创建配置文件**ZooKeeper.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"

    services:
      # ZooKeeper服务
      server:
        image: zookeeper
        ports:
          - 2181:2181
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
    ```
3. 执行部署命令`sudo docker stack deploy -c=ZooKeeper.yml ZooKeeper`;