# Ingress

> 在 Kubernetes集群中，Ingress是授权入站连接到达集群服务的规则集合，为您提供七层负载均衡能力。您可以给Ingress配置提供外部可访问的URL、负载均衡、SSL、基于名称的虚拟主机等。

## 名词解释

- **Ingress Controller**：Ingress Controller实质上可以理解为是个监视器，Ingress Controller通过不断地跟 kubernetes API打交道，实时的感知后端service、pod等变化，比如新增和减少pod，service增加与减少等；当得到这些变化信息后，Ingress Controller再结合下文的Ingress生成配置，然后更新反向代理负载均衡器，并刷新其配置，达到服务发现的作用。
- **Ingress**：简单的说，ingress就是从kubernetes集群外访问集群的入口，将用户的URL请求转发到不同的service上。Ingress相当于nginx、apache等负载均衡方向代理服务器，其中还包括规则定义，即URL的路由信息，路由信息得的刷新由Ingress controller来提供。

## 部署Ingress Controller

### nginx-ingress

1. 为k8s节点打上边缘节点角色，`kubectl label node NODE_NAME node-role.kubernetes.io/edge=`;
2. 添加仓库，`helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`;
3. 更新仓库，`helm repo update`;
4. 创建配置文件`nginx_ingress_values.yml`，并写入以下内容；

    ```yaml
    rbac:
      # 启用RBAC角色控制
      create: true
    controller:
      # 部署控制器使用DaemonSet
      kind: DaemonSet
      # 使用hostNetwork方式暴露服务
      hostNetwork: true
      service: 
        type: ClusterIP
      nodeSelector:
        node-role.kubernetes.io/edge: ''
    # 启用默认后端服务
    defaultBackend:
      enabled: true
    ```
5. 部署至k8s集群，`helm install ingress-nginx ingress-nginx/ingress-nginx -n kube-system -f nginx_ingress_values.yml`;