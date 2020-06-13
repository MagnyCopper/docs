# 部署portainer

本文以Ubuntu18.06操作系统为例。

## 准备工作

1. docker已安装至系统中；
2. 已创建docker swarm集群。

## 安装步骤

1. 创建配置文件**portainer.yml**;
2. 向配置文件写入以下内容：

    ``` yaml
    version: '3.7'

    services:
      agent:
        image: portainer/agent
        environment:
          # REQUIRED: Should be equal to the service name prefixed by "tasks." when
          # deployed inside an overlay network
          AGENT_CLUSTER_ADDR: tasks.agent
          # AGENT_PORT: 9001y
          # LOG_LEVEL: debug
        volumes:
          - /var/run/docker.sock:/var/run/docker.sock
          - /var/lib/docker/volumes:/var/lib/docker/volumes
        networks:
          - agent_network
        deploy:
          mode: global
          update_config:
            order: start-first
          placement:
            constraints: [node.platform.os == linux]
      server:
        image: portainer/portainer
        command: -H tcp://tasks.agent:9001 --tlsskipverify
        ports:
          - 9090:9000
        volumes:
          - server_data:/data
        networks:
          - agent_network
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
          placement:
            constraints: [node.role == manager]

    networks:
      agent_network:
        driver: overlay
        attachable: true

    volumes:
      server_data:
    ```
3. 执行部署命令`sudo docker stack deploy -c=portainer.yml portainer`;
4. 等待部署结束，访问`http://docker-host:9090`。