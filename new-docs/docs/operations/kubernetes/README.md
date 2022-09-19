# Kubernetes <!-- omit in toc -->

> Kubernetes（常简称为 K8s）是用于自动部署、扩展和管理“容器化（containerized）应用程序”的开源系统。

目录:

- [基础属性](#基础属性)
  - [使用 kubeadm 部署 k8s](#使用-kubeadm-部署-k8s)
- [知识点](#知识点)
  - [第一个知识点 XXXXXX](#第一个知识点-xxxxxx)

## 基础属性

### 使用 kubeadm 部署 k8s

1. 检查每台节点的 MAC 地址和 product_uuid 唯一性;

   ```bash
   ifconfig -a
   sudo cat /sys/class/dmi/id/product_uuid
   ```

2. 安装容器运行时(CRI);

   CRI-O

   ```bash
   # 禁用swap:注释/swap.img行
   sudo vim /etc/fstab
   sudo reboot
   # 启用内核模块
   sudo modprobe overlay
   sudo modprobe br_netfilter
   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.bridge.bridge-nf-call-iptables  = 1
   net.ipv4.ip_forward                 = 1
   net.bridge.bridge-nf-call-ip6tables = 1
   EOF
   sudo sysctl --system
   # 设置变量
   export VERSION=1.25
   export OS=xUbuntu_22.04
   # 添加仓库
   echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
   curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key add -
   echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
   curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | sudo apt-key add -
   # 安装
   sudo apt update
   sudo apt install cri-o cri-o-runc
   # 添加启动
   sudo systemctl enable cri-o.service
   sudo systemctl start cri-o.service
   ```

3. 安装 kubeadm ;

   ```bash
   # 安装依赖
   sudo apt update
   sudo apt install -y apt-transport-https ca-certificates curl
   #设置仓库
   sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
   echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   #安装
   sudo apt update
   sudo apt install -y kubelet kubeadm kubectl
   # 固定版本
   sudo apt-mark hold kubelet kubeadm kubectl
   ```

4. 使用 kubeadm 创建集群;

   ```bash
   sudo kubeadm init --apiserver-advertise-address=192.168.10.7
   ```

## 知识点

### 第一个知识点 XXXXXX

知识点的描述部分
