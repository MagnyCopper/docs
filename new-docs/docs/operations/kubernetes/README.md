# Kubernetes <!-- omit in toc -->

> Kubernetes（常简称为 K8s）是用于自动部署、扩展和管理“容器化（containerized）应用程序”的开源系统。

目录:

- [基础属性](#基础属性)
  - [使用 kubeadm 部署 k8s](#使用-kubeadm-部署-k8s)
- [知识点](#知识点)
  - [第一个知识点 XXXXXX](#第一个知识点-xxxxxx)

## 基础属性

### 使用 kubeadm 部署 k8s

以下内容均使用 Ubuntu22.04 操作系统

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
   sudo systemctl enable crio.service
   sudo systemctl start crio.service
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
   sudo kubeadm init --apiserver-advertise-address=192.168.10.7 --pod-network-cidr=192.168.99.0/16
   ```

5. 添加集群配置文件

   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

6. 添加网络插件

   calico 插件

   ```bash
   kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.24.1/manifests/tigera-operator.yaml
   ```

   创建配置文件`calico.yaml`,cidr 修改为与上面`--pod-network-cidr`相同的地址

   ```json
   # This section includes base Calico installation configuration.
   # For more information, see: https://projectcalico.docs.tigera.io/master/reference/   installation/api#operator.tigera.io/v1.Installation
   apiVersion: operator.tigera.io/v1
   kind: Installation
   metadata:
     name: default
   spec:
     # Configures Calico networking.
     calicoNetwork:
       # Note: The ipPools section cannot be modified post-install.
       ipPools:
       - blockSize: 26
         cidr: 192.168.99.0/16
         encapsulation: VXLANCrossSubnet
         natOutgoing: Enabled
         nodeSelector: all()

   ---

   # This section configures the Calico API server.
   # For more information, see: https://projectcalico.docs.tigera.io/master/reference/   installation/api#operator.tigera.io/v1.APIServer
   apiVersion: operator.tigera.io/v1
   kind: APIServer
   metadata:
     name: default
   spec: {}
   ```

   执行`kubectl create -f calico.yaml`安装插件

7. 允许 master 节点部署 pod

   ```bash
   sudo kubectl taint nodes --all node-role.kubernetes.io/control-plane- node-role.kubernetes.io/master-
   ```

8. 添加 worker 节点

   ```bash
   kubeadm join 192.168.10.7:6443 --token zmy28z.d8u3slbiseiknn67    --discovery-token-ca-cert-hash   sha256:1d6cffbd46bbe709c47cc891ec0dd6a1f9b5391206d2cc28504600621a68c5bf
   ```

## 知识点

### 第一个知识点 XXXXXX

知识点的描述部分
