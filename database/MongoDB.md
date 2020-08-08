# MongoDB

> MongoDB（来自于英文单词“Humongous”，中文含义为“庞大”）是可以应用于各种规模的企业、各个行业以及各类应用程序的开源数据库。作为一个适用于敏捷开发的数据库，MongoDB的数据模式可以随着应用程序的发展而灵活地更新。与此同时，它也为开发人员 提供了传统数据库的功能：二级索引，完整的查询系统以及严格一致性等等。 MongoDB能够使企业更加具有敏捷性和可扩展性，各种规模的企业都可以通过使用MongoDB来创建新的应用，提高与客户之间的工作效率，加快产品上市时间，以及降低企业成本。

## 名词解释

- **基础概念**：Mongo中的各个概念与传统SQL中各个概念的对比；

|SQL数据库|MongoDB|解释说明|
|--|--|--|
|database|database|数据库|
|table|collection|数据库表/集合|
|row|document|数据记录行/文档|
|column|field|数据字段/域|
|index|index|索引|

## 安装说明

1. 创建配置文件**MongoDB.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"
    services:
      # MongoDB服务
      server:
        image: mongo:latest
        ports:
          - 27017:27017
        volumes:
          - server_data:/data/db
        environment:
          MONGO_INITDB_ROOT_USERNAME: root
          MONGO_INITDB_ROOT_PASSWORD: tszx11hcm
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
    volumes:
      server_data:
    ```
3. 执行部署命令`sudo docker stack deploy -c=MongoDB.yml MongoDB`，开始部署MongoDB容器;
4. 等待部署结束，访问**docker-host:27017**端口即可。

## 其他说明

### 创建数据库并添加操作用户

1. 执行`use admin`切换至admin数据库，并使用`db.auth("root","password")`进行授权；
2. 执行`use DATABASE`切换至需要创建的数据库；
3. 执行`db.createUser( {user: "admin",pwd: "password",roles: [ { role: "dbOwner", db: "DATABASE" } ]})`创建该数据库的管理员角色admin用户；
4. 使用时直接试用admin/password即可操作该数据库；