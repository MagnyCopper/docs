# MySQL

> MySQL是最流行的关系型数据库管理系统，在WEB应用方面MySQL是最好的RDBMS(Relational Database Management System：关系数据库管理系统)应用软件之一。

## 部署安装(docker)

1. 创建stack配置文件,并填写以下内容,并保存为**mysql.yml**.
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

## MySQL日志

- **Error log 错误日志**:系统默认日志,主要用来记录系统错误日志,默认开启;
- **General query log 通用查询日志**:系统默认日志,主要用来记录全部查询SQL,故启用后大量占用磁盘IO,影响性能应在调试时开启;
- **Binary log 二进制日志**:系统默认日志,主要用来记录数据变化;
- **Relay log 中继日志**:系统默认日志,主要用来记录从库服务器接收主库服务器的二进制日志;
- **Slow query log 慢查询日志**:系统默认日志,主要用来记录超过指定时限的SQL语句,用于性能分析;
- **Redo log 重做日志**:InnoDB引擎日志,主要用来记录提交的事务操作记录,用于系统崩溃时的自我恢复;
- **Undo log 撤销日志**:InnoDB引擎日志,主要用来记录事务操作的逆操作,用于事务回滚操作;

### Binary log 二进制日志

应用:
- 主从复制;
- 数据恢复;
- 数据审计;

格式:
- **STATEMENT**:记录的是数据库上执行的原生SQL语
- **ROW**:记录每一行的数据内容,不受存储过程或函数的影响
- **MIXED**:默认采用基于语句的复制，一旦发现基于语句的无法精确的复制时，就会采用基于行的复制

## MySQL索引

**最左前缀法则**:索引的任何前缀都会用于查询, (col1, col2, col3)这个联合索引的所有前缀 就是(col1), (col1, col2), (col1, col2, col3), 包含这些列的查询都会启用索引查询;

**覆盖索引**:一个索引包含了（或覆盖了）满足查询语句中字段与条件的数据就叫做覆盖索引。

### 索引建立原则
- **最左前缀匹配原则**:mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
- **=和in可以乱序**:比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式
- **尽量选择区分度高的列作为索引**:索引列的值尽量不重复
- **索引列不能参与计算**:索引列在调用函数,计算,类型转换时会导致索引失效
- **尽量的扩展索引，不要新建索引**:比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可

### 聚簇索引与非聚簇索引

- **聚簇索引**:B+树的叶子节点存放完整数据,无需回查,性能极好,如主键;
- **非聚簇索引**:B+树的叶子节点存放该数据的指针,需根据指针回查完整数据(若查询字段均为索引字段既覆盖查询则无需回查完整数据);

### 索引失效情况:
- 违反最左前缀法则
- 在索引列执行计算,函数,类型转换等操作
- 范围查询(>、<、between、like)右侧的索引列
- 不等于(!=,<>)条件
- like以通配符开头（'%abc'）
- 字符串不加单引号
- or连接条件
- 错误使用order by和group by

### 分类:
- PRIMARY KEY主键索引
- UNIQUE唯一索引
- INDEX普通索引
- FULLTEXT全文索引
- 多列索引

### 索引算法:
- 哈希
- B+树

## MySQL事务

MySQL的默认隔离级别:Repeatable Read(可重读)

各个事务特性主要由事务日志及innodb引擎保证

- **原子性**:依靠Undo log实现;
- **一致性**:依靠Undo log和Redo log实现
- **持久性**:依靠Redo log实现
- **隔离性**:依靠锁机制和MVCC保证

### MVCC

> MVCC(Multi-Version Concurrency Control，多版本并发控制)

查询一些正在被另一个事务更新的行，并且可以看到它们被更新之前的值,通过数据的隐藏列和回滚日志（undo log），实现多个版本数据的共存。