# Kubernetes Dashboard

> Dashboard是基于网页的Kubernetes用户界面。您可以使用Dashboard将容器应用部署到Kubernetes集群中，也可以对容器应用排错，还能管理集群资源。您可以使用Dashboard获取运行在集群中的应用的概览信息，也可以创建或者修改Kubernetes资源（如 Deployment，Job，DaemonSet 等等）。例如，您可以对Deployment实现弹性伸缩、发起滚动升级、重启 Pod 或者使用向导创建新的应用。

## 部署Kubernetes Dashboard

1. 生成SSL证书,执行命令：`openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout ./tls.key -out ./tls.crt -subj "/CN=192.168.50.101"`，证书将生成在目录下	*tls.crt*，*tls.key*;
2. 将生成的证书文件上传至k8s集群的secret中，执行命令`kubectl -n kube-system  create secret tls tls-secret --key ./tls.key --cert ./tls.crt`;
3. 创建*kubernetes-dashboard.yaml*文件,并在文件中填写以下内容；

    ```yaml
    rbac:
      #   创建具有读取权限的角色
      clusterReadOnlyRole: true
    ingress:
      # 创建ingress
      enabled: true
      annotations:
        # ingress的匹配规则
        kubernetes.io/ingress.class: nginx
        kubernetes.io/tls-acme: 'true'
        # 由于dashboard访问需要https所以添加以下内容
        nginx.ingress.kubernetes.io/secure-backends: "true"
        nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
      # 绑定的域名
      hosts:
        - k8s.homelab.io
      # 访问的路径
      paths:
        - /
      # https证书相关设置
      tls:
        - secretName: tls-secret
          hosts:
            - k8s.homelab.io
    ```
4. 添加helm仓库地址：`helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/`;
5. 更新仓库刷新资源：`helm repo update`;
6. 执行部署命令，在k8s集群中部署dashboard服务：`helm install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard -n kube-system -f kubernetes-dashboard.yaml`；
7. 在hosts文件中添加记录将*k8s.homelab.io*指向k8s的ingress controller暴露的IP和端口；
8. 访问*https://k8s.homelab.io*即可；
9. 登陆用到的token可以从*kubernetes-dashboard-token*开头的*secret*中获取，使用到的命令如下；

    ```bash
    kubectl -n kube-system get secret | grep kubernetes-dashboard-token
    kubectl describe -n kube-system secret/kubernetes-dashboard-token-jc4xv
    ```