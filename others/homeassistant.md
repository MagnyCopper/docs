# Home Assistant

> Home Assistant 是一款基于 Python 的智能家居开源系统，支持众多品牌的智能家居设备，可以轻松实现设备的语音控制、自动化等。

## 概念解释

- **概念 A**：概念解释；

## 安装说明

1. 编辑配置文件`homeassistant.yaml`；

   ```yaml
   version: "3.7"

   services:
     # homeassistant服务
     server:
       image: homeassistant/home-assistant:latest
       ports:
         - 8123:8123
       volumes:
         - server_data:/config
       deploy:
         mode: replicated
         replicas: 1
         update_config:
           order: start-first

   volumes:
     server_data:
   ```

2. 执行命令`sudo docker stack deploy --compose-file homeassistant.yml homeassistant`；
3. 安装HACS,连接至容器内执行`wget -O - https://get.hacs.xyz | bash -`,成功后重启容器即可;

## 注意事项/其他

1. xxxxxxx；
