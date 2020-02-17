# 安装Docker

本文以Ubuntu18.06操作系统为例。

## 脚本安装

1. 在终端执行`curl -fsSL get.docker.com -o get-docker.sh`;
2. 在终端执行`sudo sh get-docker.sh`，若网络环境较差可以执行`sudo sh get-docker.sh --mirror Aliyun`使用aliyun进行加速；
3. 若不需要每次执行docker命令都使用`sudo`,可以使用`sudo usermod -aG docker your-user`命令将用户加入docker用户组。