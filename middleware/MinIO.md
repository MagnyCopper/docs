# MinIO

本文以Ubuntu18.06操作系统为例。

## 安装

### Docker部署

1. 创建配置文件**minio.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"

    services:
      # minio服务
      server:
        image: minio/minio
        ports:
          - 9000:9000
        volumes:
          - server_data:/data
        environment:
          MINIO_ACCESS_KEY: admin
          MINIO_SECRET_KEY: tszx11hcm
        command: server /data
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first

    volumes:
      server_data:
    ```
3. 执行部署命令`sudo docker stack deploy -c=minio.yml minio`;
4. 等待部署结束，访问`http://docker-host:9000`。