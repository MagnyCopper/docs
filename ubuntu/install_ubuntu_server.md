# 安装Ubuntu Server

本文以Ubuntu18.06操作系统为例。

## 准备工作

1. 下载Ubuntu Server 18.06的系统安装镜像；

## 安装步骤

1. 使用安装光盘引导计算机启动；
2. 在*Please choose your preferred language*界面选择`English`并按下回车键确认；
3. 在*Keyboard configuration*界面*Layout*和*Variant*均选择`English (US)`选项并按下回车键确认；
4. 在*Network connections*界面确认服务器的IP地址并按下回车键确认；
5. 在*Configure proxy*界面保持*proxy address*中的内容为空白的并按下回车键确认；
6. 在Configure Ubuntu archive mirror*界面中修改*mirror address*的值为`http://mirrors.aliyun.com/ubuntu`并按下回车键确认；
7. 在*Filesystem setup*界面选择`Manual`并按下回车键进入下一步；
8. 在*AVAILABLE DEVICES*列表中选择需要作为启动磁盘的设备按下回车键选择`Make Boot Device`选项，将该设备设置为启动磁盘。在启动磁盘上选择`Add Partition`选项为启动磁盘添加`/`和`/boot`挂载点。在数据磁盘上添加`/home`挂在点，完成所有配置后按下回车进行下一步。
9. 在*profile setup*界面依次输入相关信息并按下回车进行下一步。
10. 在*SSH Setup*界面启用`Install OpenSSH Server`选项，并按下回车进行下一步。
11. 在*Featured Server Snaps*界面中无需勾选任何选项，直接按下回车键下一步。
12. 等待安装过程结束，选择`Reboot`选项完成重启进入系统。