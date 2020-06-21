# Docker

> Docker是一个开放源代码软件，是一个开放平台，用于开发应用、交付（shipping）应用、运行应用。Docker允许用户将基础设施（Infrastructure）中的应用单独分割出来，形成更小的颗粒（容器），从而提高交付软件的速度。Docker容器与虚拟机类似，但原理上，容器是将操作系统层虚拟化，虚拟机则是虚拟化硬件，因此容器更具有便携性、高效地利用服务器。容器更多的用于表示软件的一个标准化单元。由于容器的标准化，因此它可以无视基础设施（Infrastructure）的差异，部署到任何一个地方。另外，Docker也为容器提供更强的业界的隔离兼容。

## 安装Docker

### Linux平台(Ubuntu)

1. 在终端执行`curl -fsSL get.docker.com -o get-docker.sh`;
2. 在终端执行`sudo sh get-docker.sh`，若网络环境较差可以执行`sudo sh get-docker.sh --mirror Aliyun`使用aliyun进行加速；
3. 若不想要每次执行docker命令都使用`sudo`,可以使用`sudo usermod -aG docker your-user`命令将用户加入docker用户组。