# MySQL

> MySQL是最流行的关系型数据库管理系统，在WEB应用方面MySQL是最好的RDBMS(Relational Database Management System：关系数据库管理系统)应用软件之一。

## 部署安装(docker)

1. 创建stack配置文件,并填写以下内容,并保存为**mysql.yml**
  ```yaml
  version: "3.7"
  services:
    # MySQL服务
    server:
      image: mysql:latest
      ports:
        # 对外暴露的端口
        - 3306:3306
      volumes:
        # 持久化路径
        - server_data:/var/lib/mysql
      environment:
        # 默认的root密码
        MYSQL_ROOT_PASSWORD: tszx11hcm
      deploy:
        mode: replicated
        replicas: 1
        update_config:
          order: start-first
  volumes:
    server_data:
  ```
2. 在命令行中执行`docker stack deploy -c mysql.yml mysql`部署容器,待容器启动后即可访问.