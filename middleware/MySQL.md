# MySQL

MySQL是一个关系型数据库管理系统，由瑞典MySQL AB公司开发，属于Oracle旗下产品。MySQL是最流行的关系型数据库管理系统之一，在WEB应用方面，MySQL是最好的RDBMS(Relational Database Management System，关系数据库管理系统)应用软件之一。

## 安装

### Docker部署

1. 创建配置文件**MySQL.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"
    services:

      # MySQL服务
      server:
        image: mysql:latest
        ports:
          - 3306:3306
        volumes:
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
3. 执行部署命令`sudo docker stack deploy -c=MySQL.yml MySQL`;
4. 等待部署结束，访问`docker-host的3306端口`。