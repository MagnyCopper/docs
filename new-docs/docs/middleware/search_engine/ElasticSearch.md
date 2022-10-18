# ElasticSearch <!-- omit in toc -->

> 这里是标题的一小段介绍,一般来自 Wiki 百科或者自己编;

目录:

- [基础属性](#基础属性)
  - [基础概念](#基础概念)
  - [ES 与 RDBMS 的概念映射](#es-与-rdbms-的概念映射)
  - [部署安装](#部署安装)
- [知识点](#知识点)
  - [ES 数据操作流程](#es-数据操作流程)
  - [ES 集群中的节点有哪些角色?](#es-集群中的节点有哪些角色)
  - [ES 集群中索引的分片有哪些类型?](#es-集群中索引的分片有哪些类型)

## 基础属性

### 基础概念

- **node 节点**:一个运行中的 Elasticsearch 实例称为一个节点;
- **集群**:由一个或者多个拥有相同 cluster.name 配置的节点组成，它们共同承担数据和负载的压力;
- **shard 分片**: 一个索引中的数据保存在多个分片中，相当于水平分表;
- **index 索引**:相当于数据库表/库的概念,用于组织管理文档;
- **document 文档**:相当于数据库的一行数据,同一个 index 下额不同文档可以有不同的 field 字段(为了性能一般是相同的);

### ES 与 RDBMS 的概念映射

|   RDBMS   |    ES    |
| :-------: | :------: |
| datatbase |  index   |
|   table   |  index   |
|    row    | document |
|  column   |  field   |
|    SQL    |   DSL    |
|  schema   | mapping  |

### 部署安装

测试环境：

1. 修改系统配置文件：`/etc/sysctl.conf` 添加`vm.max_map_count=262144`;
2. 创建配置文件`es.yaml`；
3. 添加以下内容：

   ```yaml
   version: "3.7"
   services:
     # Elasticsearch服务
     elasticsearch_server:
       image: docker.elastic.co/elasticsearch/elasticsearch:8.4.3
       ports:
         - 9200:9200
         - 9300:9300
       volumes:
         - elasticsearch_server_data:/usr/share/elasticsearch/data
       environment:
         - discovery.type=single-node
         - "ES_JAVA_OPTS=-Xms2g -Xmx2g"
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first
     # Kibana 服务
     kibana_server:
       image: docker.elastic.co/kibana/kibana:8.4.3
       ports:
         - 5601:5601
       volumes:
         - kibana_server_data:/usr/share/kibana
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first
   volumes:
     elasticsearch_server_data:
     kibana_server_data:
   ```

4. 执行命令`sudo docker stack deploy --compose-file es.yml es`;
5. 查看日志中输出 elastic 的密码或在容器中使用命令`/usr/share/elasticsearch/bin/elasticsearch-reset-password -i -u elastic --url https://localhost:9200`重设密码；

## 知识点

### ES 数据操作流程

写入数据

1. 客户端连接一个 node 发送请求,该 node 作为协调节点;
2. 协调节点对文档进行路由(根据\_routing 字段)转发给对应的主分片的数据节点;
3. 数据节点将主分片写入完毕后将写入发送至各副本分片,待副本写入完毕后将请求结果返回给协调节点;

查询数据:

1. 客户端连接一个 node 发送请求,该 node 作为协调节点;
2. 协调节点将查询请求发送至全部分片的数据节点上(过程中主副分片间负载均衡);
3. 各个数据节点将符合要求的 doc_id 返回给协调节点;
4. 协调节点再根据 doc_id 排序/合并/分页等操作后根据 id 向各个分片拉取对应的数据(过程中主副分片间负载均衡);

### ES 集群中的节点有哪些角色?

- **主节点**:负责管理集群范围内的所有变更 ，例如增加、删除索引，或者增加、删除节点等,主节点故障将自动选举新主节点;
- **数据节点**:存储数据和其对应的倒排索引;
- **协调节点**:作为接收请求、转发请求到其他节点、汇总各个节点返回数据等功能的节点，均衡每个节点的负载;

### ES 集群中索引的分片有哪些类型?

- **主 shard**:用于保存文档数据的分片,任意文档一定属于一个分片;
- **副本 shard**:主分片的副本数据,用于容灾恢复;
