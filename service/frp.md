# Frp

> frp是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp 协议，为 http 和 https应用协议提供了额外的能力，且尝试性支持了点对点穿透。

## 名词解释

- **frps**：Frp服务的服务器端；
- **frpc**：Frp服务的客户端；

## 部署说明

### 服务器端
1. 在服务器端执行命令`wget https://github.com/fatedier/frp/releases/download/v0.33.0/frp_0.33.0_linux_amd64.tar.gz`下载安装包；
2. 在服务端执行命令`tar -xvf frp_0.33.0_linux_amd64.tar.gz`解压缩安装包；
3. 重命名解压文件夹为frp：`mv frp_0.33.0_linux_amd64 frp`;
4. 创建systemctl配置文件`sudo vim /lib/systemd/system/frps.service`，并写入以下内容(注意修改**ExecStart**的部分)：

    ```ini
    [Unit]
    Description=fraps service
    After=network.target network-online.target syslog.target
    Wants=network.target network-online.target

    [Service]
    Type=simple
    #启动服务的命令（此处写你的frps的实际安装目录）
    ExecStart=/your/path/frps -c /your/path/frps.ini

    [Install]
    WantedBy=multi-user.target
    ```
5. 执行`sudo systemctl start frps`启动服务；
6. 执行`sudo systemctl enable frps`启动服务；

### 客户端
1. 在客户端执行命令`wget https://github.com/fatedier/frp/releases/download/v0.33.0/frp_0.33.0_linux_amd64.tar.gz`下载安装包；
2. 在客户端执行命令`tar -xvf frp_0.33.0_linux_amd64.tar.gz`解压缩安装包；
3. 重命名解压文件夹为frp：`mv frp_0.33.0_linux_amd64 frp`;
4. 创建systemctl配置文件`sudo vim /lib/systemd/system/frpc.service`，并写入以下内容(注意修改**ExecStart**的部分)：

    ```ini
    [Unit]
    Description=frpc service
    After=network.target network-online.target syslog.target
    Wants=network.target network-online.target

    [Service]
    Type=simple
    #启动服务的命令（此处写你的frps的实际安装目录）
    ExecStart=/your/path/frpc -c /your/path/frpc.ini

    [Install]
    WantedBy=multi-user.target
    ```
5. 执行`sudo systemctl start frpc`启动服务；
6. 执行`sudo systemctl enable frpc`启动服务；

## 命令说明

### 服务器端
- 启动服务：`sudo systemctl start frps`;
- 设置自启动：`sudo systemctl enable frps`;
- 重启服务：`sudo systemctl restart frps`;
- 停止服务：`sudo systemctl stop frps`;
- 查看服务日志：`sudo systemctl status frps`;

### 客户端
- 启动服务：`sudo systemctl start frpc`;
- 设置自启动：`sudo systemctl enable frpc`;
- 重启服务：`sudo systemctl restart frpc`;
- 停止服务：`sudo systemctl stop frpc`;
- 查看服务日志：`sudo systemctl status frpc`;

## 配置文件

### 服务器端

```ini
# [common] is integral section
[common]
# A literal address or host name for IPv6 must be enclosed
# in square brackets, as in "[::1]:80", "[ipv6-host]:http" or "[ipv6-host%zone]:80"
bind_addr = 0.0.0.0
bind_port = 7000

# udp port to help make udp hole to penetrate nat
bind_udp_port = 7001

# udp port used for kcp protocol, it can be same with 'bind_port'
# if not set, kcp is disabled in frps
kcp_bind_port = 7000

# specify which address proxy will listen for, default value is same with bind_addr
# proxy_bind_addr = 127.0.0.1

# if you want to support virtual host, you must set the http port for listening (optional)
# Note: http port and https port can be same with bind_port
vhost_http_port = 80
vhost_https_port = 443

# response header timeout(seconds) for vhost http server, default is 60s
# vhost_http_timeout = 60

# TcpMuxHttpConnectPort specifies the port that the server listens for TCP
# HTTP CONNECT requests. If the value is 0, the server will not multiplex TCP
# requests on one single port. If it's not - it will listen on this value for
# HTTP CONNECT requests. By default, this value is 0.
# tcpmux_httpconnect_port = 1337

# set dashboard_addr and dashboard_port to view dashboard of frps
# dashboard_addr's default value is same with bind_addr
# dashboard is available only if dashboard_port is set
dashboard_addr = 0.0.0.0
dashboard_port = 7500

# dashboard user and passwd for basic auth protect, if not set, both default value is admin
dashboard_user = admin
dashboard_pwd = password

# enable_prometheus will export prometheus metrics on {dashboard_addr}:{dashboard_port} in /metrics api.
enable_prometheus = true

# dashboard assets directory(only for debug mode)
# assets_dir = ./static
# console or real logFile path like ./frps.log
log_file = ./frps.log

# trace, debug, info, warn, error
log_level = info

log_max_days = 3

# disable log colors when log_file is console, default is false
disable_log_color = false

# DetailedErrorsToClient defines whether to send the specific error (with debug info) to frpc. By default, this value is true.
detailed_errors_to_client = true

# AuthenticationMethod specifies what authentication method to use authenticate frpc with frps.
# If "token" is specified - token will be read into login message.
# If "oidc" is specified - OIDC (Open ID Connect) token will be issued using OIDC settings. By default, this value is "token".
authentication_method = token

# AuthenticateHeartBeats specifies whether to include authentication token in heartbeats sent to frps. By default, this value is false.
authenticate_heartbeats = false

# AuthenticateNewWorkConns specifies whether to include authentication token in new work connections sent to frps. By default, this value is false.
authenticate_new_work_conns = false

# auth token(自定义token)
token = token

# OidcClientId specifies the client ID to use to get a token in OIDC authentication if AuthenticationMethod == "oidc".
# By default, this value is "".
oidc_client_id =

# OidcClientSecret specifies the client secret to use to get a token in OIDC authentication if AuthenticationMethod == "oidc".
# By default, this value is "".
oidc_client_secret = 

# OidcAudience specifies the audience of the token in OIDC authentication if AuthenticationMethod == "oidc". By default, this value is "".
oidc_audience = 

# OidcTokenEndpointUrl specifies the URL which implements OIDC Token Endpoint.
# It will be used to get an OIDC token if AuthenticationMethod == "oidc". By default, this value is "".
oidc_token_endpoint_url = 

# heartbeat configure, it's not recommended to modify the default value
# the default value of heartbeat_timeout is 90
# heartbeat_timeout = 90

# only allow frpc to bind ports you list, if you set nothing, there won't be any limit
allow_ports = 2000-3000,3001,3003,4000-50000

# pool_count in each proxy will change to max_pool_count if they exceed the maximum value
max_pool_count = 100

# max ports can be used for each client, default value is 0 means no limit
max_ports_per_client = 0

# TlsOnly specifies whether to only accept TLS-encrypted connections. By default, the value is false.
tls_only = false

# if subdomain_host is not empty, you can set subdomain when type is http or https in frpc's configure file
# when subdomain is test, the host used by routing is test.frps.com
# subdomain_host = magnycopper.cool

# if tcp stream multiplexing is used, default is true
tcp_mux = true

# custom 404 page for HTTP requests
# custom_404_page = /path/to/404.html
```

### 客户端

```ini
# [common] is integral section
[common]
# A literal address or host name for IPv6 must be enclosed
# in square brackets, as in "[::1]:80", "[ipv6-host]:http" or "[ipv6-host%zone]:80"
server_addr = magnycopper.cool
server_port = 7000

# if you want to connect frps by http proxy or socks5 proxy, you can set http_proxy here or in global environment variables
# it only works when protocol is tcp
# http_proxy = http://user:passwd@192.168.1.128:8080
# http_proxy = socks5://user:passwd@192.168.1.128:1080

# console or real logFile path like ./frpc.log
log_file = ./frpc.log

# trace, debug, info, warn, error
log_level = info

log_max_days = 3

# disable log colors when log_file is console, default is false
disable_log_color = false

# for authentication
token = token

# set admin address for control frpc's action by http api such as reload
admin_addr = 0.0.0.0
admin_port = 7400
admin_user = admin
admin_pwd = password
# Admin assets directory. By default, these assets are bundled with frpc.
# assets_dir = ./static

# connections will be established in advance, default value is zero
pool_count = 100

# if tcp stream multiplexing is used, default is true, it must be same with frps
tcp_mux = true

# your proxy name will be changed to {user}.{proxy}
user = frp-ubuntu-vm

# decide if exit program when first login failed, otherwise continuous relogin to frps
# default is true
login_fail_exit = false

# communication protocol used to connect to server
# now it supports tcp and kcp and websocket, default is tcp
protocol = tcp

# if tls_enable is true, frpc will connect frps by tls
tls_enable = true

# specify a dns server, so frpc will use this instead of default one
# dns_server = 8.8.8.8

# proxy names you want to start seperated by ','
# default is empty, means all proxies
# start = ssh,dns

# heartbeat configure, it's not recommended to modify the default value
# the default value of heartbeat_interval is 10 and heartbeat_timeout is 90
# heartbeat_interval = 30
# heartbeat_timeout = 90

# additional meta info for client
meta_var1 = 123
meta_var2 = 234

[homelab.router]
type = http
local_ip = 192.168.50.1
local_port = 80
use_encryption = true
use_compression = true
custom_domains = router.homelab.magnycopper.cool
remote_port = 80
```