# 安装Rancher

部署Rancher有多种方式

- 在高可用的Kubernetes安装Rancher
- 在单节点的Kubernetes安装Rancher
- 在Docker安装Rancher

## 在Docker安装Rancher

1. 创建配置文件**rancher.yml**;
2. 向配置文件写入以下内容：

    ``` yml
    version: "3.7"

    services:
      # rancher服务
      server:
        image: rancher/rancher:latest
        ports:
          - 980:80
          - 9443:443
        volumes:
          - server_data:/var/lib/rancher
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first

    volumes:
      server_data:
    ```
3. 执行部署命令`sudo docker stack deploy -c=rancher.yml rancher`;
4. 等待部署结束，访问`https://docker-host:9443`。

