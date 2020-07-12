# Kubernetes

> Kubernetes（常简称为K8s）是用于自动部署、扩展和管理“容器化（containerized）应用程序”的开源系统。该系统由Google设计并捐赠给Cloud Native Computing Foundation（今属Linux基金会）来使用。它旨在提供“跨主机集群的自动部署、扩展以及运行应用程序容器的平台”。它支持一系列容器工具, 包括Docker等。CNCF于2017年宣布首批Kubernetes认证服务提供商（KCSPs），包含IBM、华为、MIRANTIS等服务商。 

## 安装Kubernetes

### kubeadm方式

1. 安装Docker,具体流程参见Docker章节;
2. 安装kubeadm、kubelet和kubectl

    ```bash
    sudo apt update && sudo apt install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    
    # 修改成仓库文件
    cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF

    sudo apt update
    sudo apt install -y kubelet kubeadm kubectl
    ```
3. 修改Docker驱动为overlay2;

    ```bash
    sudo touch /etc/docker/daemon.json
    sudo vim /etc/docker/daemon.json
   
    # 写入以下内容
    {
        "exec-opts": [
            "native.cgroupdriver=systemd"],
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
4. 修改DNS配置,执行命令;

    ```bash
    # 临时修改DNS
    sudo vim /etc/resolv.conf
    nameserver 223.5.5.5 223.6.6.6
    # 永久修改DNS
    sudo apt install resolvconf
    sudo vim /etc/resolvconf/resolv.conf.d/base
    # 插入DNS配置
    nameserver 223.5.5.5
    nameserver 223.6.6.6
    # 应用配置
    sudo resolvconf -u
    sudo reboot
    ```
5. 禁用swarp分区,执行命令:

    ```bash
    # 临时禁用（重启失效）
    sudo swapoff -a
    # 若要永久禁用swarp
    sudo vim /etc/fstab
    # 注释以下内容
    /swap.img       none    swap    sw      0       0
    ```
6. 初始化k8s集群的Master节点,执行命令:`sudo kubeadm init --pod-network-cidr=192.168.0.0/16`,参数`--pod-network-cidr`是用来设置pod使用的网段,应避免使用局域网真实IP网段;
7. 配置kubectl工具；

    ```bash
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ```

8. 执行命令,修改K8s的调度策略,允许Master节点执行任务`kubectl taint nodes --all node-role.kubernetes.io/master-`;
9. k8s集群的Slave节点,执行命令:`sudo kubeadm join 192.168.50.101:6443 --token 58f08t.bbiigeglrb7w9bp2 --discovery-token-ca-cert-hash sha256:8d8ccca3cab952d54a6269d7ccec0dbd26aedd0c6468637b2df97596ec489ae2`将Slave节点加入集群；
10. 安装集群网络插件,执行命令:`kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml`;
11. 等待集群准备就绪,使用`kubectl get nodes`和`kubectl get pods --all-namespaces`进行检查进度;