---
title: MySQL安装教程
description: 在 Windows 和 Linux 上安装 MySQL 服务
tags:
  - mysql
Author: Yuzh
data: 2023-05-16
---

## 1、Windows安装MySQL

>基于 win11，安装MySQL

**方式一：图形化界面安装**

下载地址：[https://dev.mysql.com/downloads/installer/](https://dev.mysql.com/downloads/installer/)

![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163021856.png)

下载完成后是 msi 文件，双击后进入安装程序，根据窗口提示进行安装，即可。

**方式二：手动安装**

下载地址：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163102776.png)

下载完成后为 zip 压缩包，mysql-8.0.33-winx64.zip，解压到指定目录，如`D:\mysql-8.0.33`目录下。

配置环境变量
```
# 新增 MYSQL_HOME 变量
MYSQL_HOME=D:\mysql-8.0.33
# 添加到 path 变量
%MYSQL_HOME%\bin
```

修改MySQL默认设置
在 mysql 的安装目录(`D:\mysql-8.0.33\`)下创建 my.ini 文件，文件代码如下所示：
```
[mysqld]  
#设置 3306 端口，不配置默认3306
port = 3306

# 设置 mysql 的安装目录  
basedir=D:\mysql-8.0.33

# 设置 mysql 数据库的数据的存放目录  
datadir=D:\mysql-8.0.33\data

# 允许最大连接数  
max_connections=200

# 允许连接失败的次数
max_connect_errors=10

# 服务端使用的字符集默认为 8 比特编码的 latin1 字符集  
character-set-server=utf8mb4

# 创建新表时将使用的默认存储引擎  
default-storage-engine=INNODB

# 默认使用“mysql_native_password”插件认证
#mysql_native_password
default_authentication_plugin=mysql_native_password

[mysql]  
# 设置 mysql 客户端默认字符集  
default-character-set=utf8mb4

[client]  
# 设置 mysql 客户端连接服务端时默认使用的端口  
port=3306  
default-character-set=utf8mb4
```

安装 MySQL 为windows系统服务

```
# 以管理员身份运行 cmd 窗口：
mysqld --install
# 或者
mysqld --install MySQL --defaults-file="D:\mysql-8.0.33\my.ini"

# 执行命令后，提示：Service successfully installed. 表示安装成功
```

初始化 MySQL

- 方式一：创建一个具有空密码的 root 用户
	```
	mysqld --initialize-insecure
	```
- 方式二：创建一个具有默认的初始密码 root 用户
	```
	mysqld --initialize --console
	
	# 执行完成后，会输出 root 用户的初始默认密码，需要记住这个密码 lGXsv0Sug,jH
	# 会创建 my.ini 文件中的 data 目录（注意此目录不要手动创建）
	```

启动 mysql系统服务

```
# 以管理员身份运行cmd窗口，使用以下命令启动 mysql 服务
net start mysql
```

登录 MySQL，并修改密码

```
mysql -u root -p
# 输入生成的密码，即可进入MySQL

# 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_ps';
```

停止 MySQL 服务：
```
net stop MySQL
```

删除 MySQL 系统服务：
```
# 以管理员身份运行 cmd 窗口
sc delete mysql
```

## 2、Ubuntu安装MySQL

>基于 Ubuntu22.04，使用 apt 命令安装mysql-server

在开始安装前，先更新一下系统。命令如下：
```bash
sudo apt update
sudo apt upgrade
```

使用 apt 自动安装MySQL8：
```bash
sudo apt install mysql-server
```

安装完成后，查看MySQL的状态：
```bash
systemctl status mysql
```

![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163158598.png)

其他管理 MySQL 服务的命令：
```bash
# 查看MySQL版本
mysql --version
# 查看MySQL的服务状况
sudo systemctl status mysql.service
# 启动MySQL的服务
sudo systemctl start mysql.service
# 重启MySQL的服务
sudo systemctl restart mysql.service
# 停止MySQL的服务
sudo systemctl stop mysql.service
# MySQL服务开机不自起
sudo systemctl disable mysql.service
```

第一次需要以管理员身份进入MySQL，并修改密码：
```bash
# 免密登录MySQL
sudo mysql -uroot
# 修改 root 用户的登录密码
alter user 'root'@'localhost' identified with mysql_native_password by 'your_new _password';
  
# 后续就可以使用 mysql -u root -p回车，输入密码的方式登录
```

授权远程使用：
```bash
# 方式一：直接给root用户远程登录
# ---1、root账号的host修改为可以访问所有主机
use mysql
update user set host='%' where user='root';
# ---2、先刷新权限，再授权
flush privileges;
grant all on *.* to 'root'@'%';
# ---3、刷新权限
flush privileges;
# ---4、在/etc/mysql/mysql.conf.d/mysqld.cnf中，将绑定本地地址注释/修改，然后保存
bind-address		= 0.0.0.0
mysqlx-bind-address	= 0.0.0.0

# 方式二：新建一个用户用于远程登录
```

## 3、Centos安装MySQL

>基于 Centos7.9，使用 bundle.tar 包进行安装

卸载自带的 MariaDB 数据库
```bash
# 1、查看版本
rpm -qa|grep mariadb

# 2、卸载
rpm -e --nodeps 文件名

# 3、检查是否卸载干净
rpm -qa|grep mariadb
```

官网下载 MySQL 安装包
地址：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)
![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163249573.png)

解压下载好的 MySQL 安装包
```bash
mkdir /opt/mysql80
tar -xvf mysql-8.0.31-1.el7.x86_64.rpm-bundle.tar -C /opt/mysql80/
```
![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163331824.png)

开始安装
```bash
cd /opt/mysql80
# 按顺序安装，若顺序不对，可能会出现依赖错误
rpm -ivh mysql-community-common-8.0.31-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-plugins-8.0.31-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.31-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.31-1.el7.x86_64.rpm
rpm -ivh mysql-community-icu-data-files-8.0.31-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.31-1.el7.x86_64.rpm
```

安装过程可能出现错误，需要安装一下工具：
```bash
yum install libaio
yum install net-tools
```

初始化MySQL设置：
```bash
# 初始化
mysqld --initialize --user=mysql

# 启动服务
systemctl start mysqld.service

# 查看启动的服务
systemctl list-unit-files --type=service


# 设置开机自启动
systemctl enable mysqld.service

# 查看是否开机自启动
systemctl list-unit-files | grep mysqld.service
```

首次登录：
```bash
# 查看初始的密码
cat /var/log/mysqld.log | grep password
Tk6Qza%RLaZF

# 登录
mysql -u root -p

# 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'passwd';

# 退出，重新登陆即可生效
```

## 4、防火墙设置
```bash
# 查看防火墙状态
firewall-cmd --state

# 启动防火墙
systemctl start firewalld.service

# 关闭防火墙
systemctl stop firewalld.service

# 设置开机自启动
systemctl enable firewalld.service

# 开放防火墙端口：3306
firewall-cmd --zone=public --add-port=3306/tcp --permanent

# 重新加载配置
firewall-cmd --reload

# 查看开放的防火墙端口
firewall-cmd --zone=public --list-ports
```

## 5、数据库管理软件

**Navicat16**

付费软件，地址：[https://navicat.com.cn/products](https://navicat.com.cn/products)
无限刷新使用时间，删除一下两个文件、文件夹即可
```bash
rm -rf ~/.config/navicat
rm -rf ~/.config/dconf/user
```

**DBeaver**

开源免费软件，地址：[https://dbeaver.io/download/](https://dbeaver.io/download/)

**Beekeeper Studio**

地址：[https://github.com/beekeeper-studio/beekeeper-studio](https://github.com/beekeeper-studio/beekeeper-studio)