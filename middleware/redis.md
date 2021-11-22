# Redis

> Redis 是一个使用 ANSI C 编写的开源、支持网络、基于内存、分布式、可选持久性的键值对存储数据库。

## 名词解释

- **名词 A**：名词解释；

## 安装说明

1. 编辑配置文件`redis.yaml`,并填写以下内容:

   ```yaml
   version: "3.7"

   services:
     # Redis服务
     server:
       image: redis:latest
       ports:
         - 6379:6379
       volumes:
         - server_data:/data
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first

   volumes:
     server_data:
   ```

2. 执行命令`sudo docker stack deploy --compose-file redis.yml vossibility`；

## 知识点

### Redis 的数据类型

- string（字符串）
- hash（哈希）
- list（列表）
- set（集合）
- zset(sorted set：有序集合)
- stream（流）
