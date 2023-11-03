---
title: docker搭建开发环境
description: 记录在 ArchLinux 上使用 Docker 搭建我的开发环境
tags:
  - docker
Author: Yuzh
data: 2023-10-01
---

## 1、docker安装

使用 Pacman 安装：
```bash
sudo pacman -S docker
```

启动 Docker：
```bash
sudo systemctl enable docker
sudo systemctl start docker

sudo systemctl status docker
```

把工作用户加入 docker 组，避免使用 root 帐号工作：
```bash
sudo usermod -aG docker $USER
newgrp docker
# 重启 doker 服务
```

配置镜像加速：
```bash
# 修改 `/etc/docker/daemon.json`，添加如下内容：
{
    "registry-mirrors": [
	    "https://docker.mirrors.ustc.edu.cn",
	    "https://hub-mirror.c.163.com",
	    "https://registry.docker-cn.com",
    ]
    
}
```

## 2、docker安装MySQL

```bash
docker pull mysql:8.0.30

mkdir ~/mysql 
docker run -d \
  --name mysql8 \
  -e MYSQL_ROOT_PASSWORD=<your_root_password> \
  -p 3306:3306 \
  -v /home/zyu/mysql:/var/lib/mysql \
  mysql:8.0.30
```

## 3、docker安装Redis

```bash
docker pull redis:6.2.13

mkdir ~/redis
docker run -d \
  --name redis6 \
  -p 6379:6379 \
  -v /home/zyu/redis:/data \
  redis:6.2.13
```