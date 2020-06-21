# Storage

> 容器中的文件在磁盘上是临时存放的，这给容器中运行的特殊应用程序带来一些问题。首先，当容器崩溃时，kubelet将重新启动容器，容器中的文件将会丢失——因为容器会以干净的状态重建。其次，当在一个Pod中同时运行多个容器时，常常需要在这些容器之间共享文件。Kubernetes抽象出Volume对象来解决这两个问题。

## 名词解释

- **Persistent Volumes(PV)**：集群中已由管理员配置的一段网络存储。集群中的资源就像一个节点是一个集群资源。 PV是诸如卷之类的卷插件，但是具有独立于使用PV的任何单个pod的生命周期。 该API对象捕获存储的实现细节，即NFS，iSCSI或云提供商特定的存储系统。；
- **Persistent Volume Claims(PVC)**：是用户存储的请求。它类似于pod。Pod消耗节点资源，PVC消耗存储资源。pod可以请求特定级别的资源（CPU和内存）。权限要求可以请求特定的大小和访问模式；
- **Storage Classes**：Storage Class为管理员提供了描述存储"类"的方法。不同的类型可能会映射到不同的服务质量等级或备份策略，或是由集群管理员制定的任意策略。Kubernetes本身并不清楚各种类代表的什么。这个类的概念在其他存储系统中有时被称为 "配置文件";

## 配置说明

### Persistent Volume Claims(PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  # 访问模式
  accessModes:
    - ReadWriteOnce
  # 磁盘模式
  volumeMode: Filesystem
  # 需求资源，磁盘空间大小等
  resources:
    requests:
      storage: 8Gi
  # 使用的storageClass名称
  storageClassName: slow
```

### Storage Classes

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
# 用来决定使用哪个卷插件分配PV
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
# 卷回收策略
reclaimPolicy: Delete
# 是否允许拓展卷
allowVolumeExpansion: true
mountOptions:
  - debug
volumeBindingMode: Immediate
```

- **parameters**:根据不同的插件由差异，通常用配置插件的工作方式，连接信息等；
- **reclaimPolicy**:
    - Retain：不清理, 保留Volume（需要手动清理）；
    - Recycle：删除数据，即 rm -rf /thevolume/*（只有 NFS 和 HostPath 支持）；
    - Delete：删除存储资源，比如删除 AWS EBS 卷（只有 AWS EBS, GCE PD, Azure Disk 和 Cinder 支持）；

## 说明

### 创建NFS动态卷

1. 登陆k8s集群的所有节点，安装nfs-utils工具，执行命令：`sudo apt install -y nfs-common`;
2. 添加stable仓库，执行命令：`helm repo add stable https://kubernetes-charts.storage.googleapis.com`;
3. 执行`helm repo update`更新仓库;
4. 创建helm的配置文件，**nfs-client-provisioner.yaml**，并在其中写入以下内容；
    ```yaml
    storageClass: 
      # 设置为默认的storageClass
      defaultClass: true
      # 删除PVC时是否保留数据
      archiveOnDelete: false
    nfs: 
      server: 192.168.50.11
      path: /volume1/NFS
    ```
5. 部署nfs-client-provisioner,执行命令`helm install nfs-client-provisioner stable/nfs-client-provisioner -n kube-system -f nfs-client-provisioner.yaml`;

### 使用动态卷部服务

1. 创建测试用的配置文件**test-storageclass.yaml**,并写入以下内容；

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: nfs-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      volumeMode: Filesystem
      resources:
        requests:
          storage: 1Gi
      storageClassName: nfs-client
    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: storage-class-test
    spec:
      containers:
        - name: storage-class-test
          image: busybox:latest
          command:
            - "/bin/sh"
          args:
            - "-c"
            - "touch /mnt/SUCCESS && exit 0 || exit 1"
          volumeMounts:
            - name: nfs-pv
              mountPath: "/mnt"
      volumes:
        - name: nfs-pv
          persistentVolumeClaim:
            claimName: nfs-pvc
    ```
2. 执行命令创建测试资源`kubectl apply -f test-storageclass.yaml`，等待系统提示资源创建成功；
3. 观察NFS服务器上是否创建了名为**SUCCESS**的文件,若该文件出现则说明StorageClass工作正常；
4. 执行命令`kubectl delete -f test-storageclass.yaml`清理测试环境；