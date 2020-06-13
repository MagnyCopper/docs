# Docker Swarm管理

## Q&A

### 1.初始化Docker Swarm集群

1. 执行命令:`sudo docker swarm init`即可;

### 2.加入Docker Swarm集群

1. 执行命令:`sudo docker swarm join --token TOKEN MASTER_HOST:2377`即可;
2. 若忘记TOKEN,可执行:`sudo docker swarm join-token worker`查看加入集群的命令;



