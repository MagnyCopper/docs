# Redis

> Redis 是一个使用 ANSI C 编写的开源、支持网络、基于内存、分布式、可选持久性的键值对存储数据库.

## 名词解释

- **基础数据结构**: String、Hash、List、Set、SortedSet；
- **特殊数据结构**: HyperLogLog、Geo、Pub/Sub;

## 部署

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
