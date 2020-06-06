# Consul

> Consul是一个支持多数据中心分布式高可用的服务发现和配置共享的服务软件,由HashiCorp公司用Go语言开发, 基于Mozilla Public License 2.0的协议进行开源.Consul支持健康检查,并允许HTTP和DNS协议调用API存储键值对.一致性协议采用Raft算法,用来保证服务的高可用.使用GOSSIP协议管理成员和广播消息,并且支持ACL访问控制.

## 安装

### Docker部署

1. 创建配置文件**consul.yml**；
2. 向配置文件写入如下内容：
    ```yml
    version: "3.7"
    
    services:
      # consul
      server:
        image: consul:latest
        ports:
          - 8500:8500
        volumes:
          - server_data:/consul/data
        environment:
          CONSUL_BIND_INTERFACE: eth0
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
    
    volumes:
      server_data:
    ```
3. 执行部署命令`sudo docker stack deploy -c=consul.yml consul`;
4. 等待部署结束，访问`http://docker-host:8500`。