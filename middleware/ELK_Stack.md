# ELK Stack

什么是 ELK Stack?很简单,指的就是 Elastic Stack.
“ELK”是三个开源项目的首字母缩写，这三个项目分别是：Elasticsearch、Logstash 和 Kibana。Elasticsearch 是一个搜索和分析引擎。Logstash 是服务器端数据处理管道，能够同时从多个来源采集数据，转换数据，然后将数据发送到诸如 Elasticsearch 等“存储库”中。Kibana 则可以让用户在 Elasticsearch 中使用图形和图表对数据进行可视化。

## 安装

### Docker部署

1. 修改运行ES的主机配置:执行```sudo sysctl -w vm.max_map_count=262144```修改配置；
2. 创建配置文件**ELK_Stack.yml**；
2. 向配置文件写入如下内容：

    ``` yml
    version: "3.7"
    services:

      # Elasticsearch服务
      elasticsearch:
        image: elasticsearch:7.6.1
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
        image: kibana:7.6.1
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
3. 执行部署命令`sudo docker stack deploy -c=ELK_Stack.yml ELK_Stack`;
4. 等待部署结束，访问`http://docker-host:5601`。