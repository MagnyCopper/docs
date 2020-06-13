# Helm

> Helm helps you manage Kubernetes applications — Helm Charts help you define, install, and upgrade even the most complex Kubernetes application.

## 安装

1. 在能够使用kubectl命令访问集群的机器上下载脚本`curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3`;
2. 为下载的脚本赋予执行权限`chmod 700 get_helm.sh`
3. 执行安装命令`./get_helm.sh`