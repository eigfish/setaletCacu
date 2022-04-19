需要准备的工具：ssh客户端 一台vps 一个onedrive账号
以下教程使用Ubuntu 20.04系统演示
其他Linux系统请替换对应命令

###配置安装环境###
切换到root用户
sudo -i
安装系统更新以及必备软件
apt update && apt install git wget curl fuse -y
###配置安装环境###

###获取OneDrive Token###
需要在windows PowerShell/CMD环境下执行
rclone下载地址
https://rclone.org/downloads/
运行命令
./rclone authorize "onedrive"
###获取OneDrive Tok


###rclone安装/配置###
安装rclone
curl https://rclone.org/install.sh | sudo bash
新建rclone配置文件
rclone config


创建本地文件夹用于挂载
mkdir /home/OneDrive
设置rclone开机自启服务
command="mount od:/ /home/OneDrive --allow-other --attr-timeout 5m --vfs-cache-mode full --vfs-cache-max-age 24h --vfs-cache-max-size 20G --vfs-read-chunk-size-limit 100M --buffer-size 100M --umask 000"
#复制整段#
cat > /etc/systemd/system/rclone.service <<EOF
[Unit]
Description=Rclone
After=network-online.target

[Service]
Type=simple
ExecStart=$(command -v rclone) ${command}
Restart=on-abort
User=root

[Install]
WantedBy=default.target
EOF
#复制整段#
重新加载服务
systemctl daemon-reload
rclone服务开启开机自启
systemctl enable rclone
启动rclone服务
systemctl start rclone
显示磁盘空间信息，查看是否挂载成功
df -h
###rclone安装/配置###

###Aria2安装/配置###
下载aria2安装脚本
wget -N git.io/aria2.sh && chmod +x aria2.sh
执行安装脚本
./aria2.sh
设置aria2下载路径为OneDrive挂载到服务器的路径
########################Aria2 简单配置信息：

 IPv4 地址      

设置Tracker Lists
https://github.com/XIU2/TrackersListCollection/blob/master/README-ZH.md
###Aria2安装/配置###

###部署可道云###
安装海外版宝塔面板
wget -O install.sh http://www.aapanel.com/script/install-ubuntu_6.0_en.sh && bash install.sh forum
安装Nginx 1.17+PHP 7.3，完成后安装PHP Redis拓展
下载可道云源码
可道云github地址 https://github.com/kalcaddle/kodbox/releases
###部署可道云###

###其他命令###
fusermount -qzu od #取消挂载名为od的磁盘
systemctl stop rclone #停止rclone服务
systemctl disable rclone #关闭rclone服务开机自启
rclone mount od:/ /home/OneDrive --allow-other --attr-timeout 5m --vfs-cache-mode full --vfs-cache-max-age 24h --vfs-cache-max-size 10G --vfs-read-chunk-size-limit 100M --buffer-size 100M --daemon #rclone临时挂载名为od的磁盘到本地/home/OneDrive目录，重启服务器后失效
###其他命令###

###注意事项###
强烈建议申请一个免费域名配合ssl证书使用
刷新页面之后函数还是没有启用的话重启一下PHP
宝塔面板/Aria2安装后打不开请放行指定端口
"od"为我在rclone设置的磁盘名称，"/home/OneDrive"为rclone挂载到服务器的本地位置，可以根据个人情况自定义
###注意事项###
