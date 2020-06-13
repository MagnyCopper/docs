# Kubernetes

> Kubernetes（常简称为K8s）是用于自动部署、扩展和管理“容器化（containerized）应用程序”的开源系统。该系统由Google设计并捐赠给Cloud Native Computing Foundation（今属Linux基金会）来使用。它旨在提供“跨主机集群的自动部署、扩展以及运行应用程序容器的平台”。它支持一系列容器工具, 包括Docker等。CNCF于2017年宣布首批Kubernetes认证服务提供商（KCSPs），包含IBM、华为、MIRANTIS等服务商。 

## 安装Kubernetes

### kubeadm方式

1. 安装Docker,具体流程参见Docker章节;
2. 确保iptables工具不使用nftables后端,仅针对Debian 10 (Buster)、Ubuntu 19.04、Fedora 29 和较新的发行版本,执行命令;

    ```shell
    sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
    sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
    sudo update-alternatives --set arptables /usr/sbin/arptables-legacy
    sudo update-alternatives --set ebtables /usr/sbin/ebtables-legacy
    ```
3. 安装kubeadm、kubelet和kubectl

    ```shell
    sudo apt update && sudo apt install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    sudo apt update
    sudo apt install -y kubelet kubeadm kubectl
    ```
4. 修改Docker驱动;

    ```shell
    sudo touch /etc/docker/daemon.json
    sudo vim /etc/docker/daemon.json
    {
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2"
    }
    sudo mkdir -p /etc/systemd/system/docker.service.d
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```
5. 修改DNS配置,执行命令;

    ```shell
    # 临时修改DNS
    sudo vim /etc/resolv.conf
    nameserver 223.5.5.5 223.6.6.6
    # 永久修改DNS
    sudo vim /etc/systemd/resolved.conf
    [Resolve]
    DNS=223.5.5.5 223.6.6.6
    systemctl restart systemd-resolved.service
    sudo reboot
    ```
6. 禁用swarp分区,执行命令:`sudo swapoff -a`;
7. 初始化k8s集群的Master节点,执行命令:`sudo kubeadm init --pod-network-cidr=192.168.0.0/16`,参数`--pod-network-cidr`是用来设置pod使用的网段,应避免使用局域网真实IP网段;
8. 配置kubectl工具,免除使用root权限

    ```shell
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```

9. 执行命令,修改K8s的调度策略,允许Master节点执行任务`kubectl taint nodes --all node-role.kubernetes.io/master-`;
10. k8s集群的Slave节点,执行命令:`sudo kubeadm join 192.168.50.101:6443 --token 58f08t.bbiigeglrb7w9bp2 --discovery-token-ca-cert-hash sha256:8d8ccca3cab952d54a6269d7ccec0dbd26aedd0c6468637b2df97596ec489ae2`;将Slave节点加入集群
11. 安装集群网络插件,执行命令:`kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml`;
12. 等待集群准备就绪,使用`kubectl get nodes`和`kubectl get pods --all-namespaces`进行检查进度;