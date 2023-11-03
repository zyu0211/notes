---
title: Redis安装教程
description: 记录在各种 Linux 发行版上安装 Redis
tags:
  - redis
Author: Yuzh
data: 2023-05-16
---

## Ubuntu安装Redis

>基于 Ubuntu22.04，使用 apt 命令安装redis-server

在开始安装前，先更新一下系统。命令如下：
```bash
sudo apt update
sudo apt upgrade
```

使用 apt 自动安装redis：
```bash
sudo apt install redis-server
```

安装完成后，查看MySQL的状态：
```bash
systemctl status redis
```
![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163803468.png)


配置Redis远程设置：
```bash
# 如果需要远程访问Redis，则需要修改Redis的配置文件
# 默认配置文件为/etc/redis/redis.conf  
sudo vim /etc/redis/redis.conf

# 修改信息
# 注释掉绑定地址#bind 127.0.0.1，让Redis可远程访问
bind 127.0.0.1 ::1

#取消注释 requirepass 启动密码认证，并设置访问密码
requirepass your-pass-word

# 以守护进程运行Redis 
daemonize yes

# 配置完成后，保存并关闭文件，然后重启Redis
```

## Centos安装Redis

>基于 Centos7.9，使用 tar 包进行安装

官网下载tar包，地址：[https://redis.io/download/#redis-downloads](https://redis.io/download/#redis-downloads)

下载压缩包：redis-6.2.7.tar.gz

解压到指定目录：
```bash
tar -zxvf redis-6.2.7.tar.gz -C /opt/
```

编译安装：
```bash
cd /opt/redis-6.2.7/

# 编译
make

# 安装
# 可以使用 PREFIX 自定义安装路径，默认在/usr/local/bin/
make install [PREFIX=/tools/redis]
```
若编译过程报错，需要安装gcc：`yum insall gcc`
![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163844541.png)

启动服务
```bash
# 开启服务
redis-server /opt/redis-6.2.7/redis.conf

# 进入交互式命令行
redis-cli
```

配置文件设置
```
# 设置后台启动，如果不是后台启动，每次退出终端redis就关闭了
daemonize yes

# 开启密码保护，注释则不需要密码
requirepass 密码

# 设置端口号
port 6379

# 允许访问的ip，改为0.0.0.0就是所有ip均可
bind 127.0.0.1 -::1
bind 0.0.0.0

# 工作目录，默认是当前目录，日志、持久化等文件会保存在这个目录
dir .

# 数据库数量，设置为1，代表只使用1个库，默认有16个库，编号0~15
databases 1

# 设置redis能够使用的最大内存
maxmemory 512mb

# 日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```

设置开机自启动
```
# 新建一个系统服务文件：
vim /etc/systemd/system/redis.service

# 写入如下内容：
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /opt/redis-6.2.7/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target

# 重载系统服务
systemctl daemon-reload

# 然后就可以使用systemctl命令
# 启动
systemctl start redis
# 停止
systemctl stop redis
# 重启
systemctl restart redis
# 查看状态
systemctl status redis
# 开机自启
systemctl enable redis
```