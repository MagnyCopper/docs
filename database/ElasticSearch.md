# ElasticSearch

> Elasticsearch 是一个分布式、高扩展、高实时的搜索与数据分析引擎。它能很方便的使大量数据具有搜索、分析和探索的能力。充分利用Elasticsearch的水平伸缩性，能使数据在生产环境变得更有价值。Elasticsearch 的实现原理主要分为以下几个步骤，首先用户将数据提交到Elasticsearch 数据库中，再通过分词控制器去将对应的语句分词，将其权重和分词结果一并存入数据，当用户搜索数据时候，再根据权重将结果排名，打分，再将返回结果呈现给用户。

## 名词解释

- **Node与Cluster**：Elastic 本质上是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elastic 实例。单个 Elastic 实例称为一个节点（node）。一组节点构成一个集群（cluster）。
- **Index**：Elastic 会索引所有字段，经过处理后写入一个反向索引（Inverted Index）。查找数据的时候，直接查找该索引。所以，Elastic 数据管理的顶层单位就叫做 Index（索引）。它是单个数据库的同义词。每个 Index （即数据库）的名字必须是小写。
- **Document**：Index 里面单条的记录称为 Document（文档）。许多条 Document 构成了一个 Index。

## 安装

### Docker部署

1. 执行命令修改内核参数`sudo sysctl -w vm.max_map_count=262144`
2. 创建配置文件**ElasticSearch.yml**；
3. 向配置文件写入如下内容：
    ```yml
    version: "3.7"
    services:
      # Elasticsearch服务
      elasticsearch:
        image: elasticsearch:7.9.3
        ports:
          - 9300:9300
          - 9200:9200
        volumes:
          - elasticsearch_data:/usr/share/elasticsearch/data
        environment:
          - "discovery.type=single-node"
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
      # kibana服务
      kibana:
        image: kibana:7.9.3
        ports:
          - 5601:5601
        deploy:
          mode: replicated
          replicas: 1
          update_config:
            order: start-first
    volumes:
      elasticsearch_data:
    ```
4. 执行部署命令`sudo docker stack deploy -c=ElasticSearch.yml ElasticSearch`;
5. 等待部署结束，访问`http://docker-host:5601`。

## 注意事项/其他

1. xxxxxxx；