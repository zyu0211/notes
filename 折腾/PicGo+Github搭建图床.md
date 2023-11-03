---
title: 图床搭建
description: 使用 picgo 在 github 上搭建个人图床
tags:
  - picgo
Author: Yuzh
data: 2023-09-15
---

## 1、配置Github

1. 创建一个新仓库，用于存放图片，如：zyu0211/img-repository。（仓库必须是public）
2. 生成一个token，用于 picGo 访问 github：
	- 头像 --> Settings --> Developer Settings --> Personal access tokens --> Tokens(classic) --> Generate new token --> 勾选repo选项 --> 生成token --> 复制生成的token （需要记住，后续不会显示）

## 2、配置PicGo

Github项目地址：[https://github.com/Molunerfinn/PicGo](https://github.com/Molunerfinn/PicGo)

启动 PicGo 后，在 **图床设置/Github图床** 选项中进行配置：

1. 图床配置名：随意
2. 设定仓库名：github用户名/仓库名，如 `zyu0211/img-repository`
3. 设定分支名：main，不填则默认为 master
4. 设定Token：复制上面步骤生成的token
5. 设置存储路径：img/，不填则默认将图片上传到仓库根目录
6. 设置自定义域名：`https://cdn.jsdelivr.net/gh/[github用户名]/[仓库名]@main`，如：`https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main`