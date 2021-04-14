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
