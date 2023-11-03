---
title: MySQL主从复制
description: 基于虚拟机搭建MySQL一主一从服务架构
tags:
  - mysql
Author: yuzh
data: 2023-10-15
---

## 1. MySQL主从复制原理

MySQL的主从复制中主要有三个线程：

-   主服务器：binlog dump thread
-   从服务器：I/O thread
-   从服务器：SQL thread

![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163511290.png)


**过程**：

1.  master将改变记录到**二进制日志**(bin log)
2.  slave将master的bin log 拷贝到它的**中继日志**(relay log)
3.  slave重做中继日志中的事件，将改变应用到自己的数据库中

## 2. 主从搭建

准备：两台服务器，分别安装MySQL并启动服务

-   主库Master：192.168.131.100
-   从库Slave：191.168.131.101

**配置主库Master**：

1.  修改MySQL的配置文件：`/etc/my.cnf`
    ```
	[mysqld]  
	
    #启用二进制日志  
    log-bin=mysql-bin
	
	# 服务唯一ID  
    server-id=100
	```

2.  重启MySQL服务
    ```
    systemctl restart mysqld
    # 进入MySQL查看log_bin日志是否启动成功
    show variables like '%log_bin%';
	```
    ![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163627567.png)

3.  创建一个用户用于同步数据
    ```mysql
    create user 'backup'@'%' IDENTIFIED BY 'backup';  
    grant file on *.* to 'backup'@'%';  
    GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* to 'backup'@'%';
	```

4.  查看Master的状态，执行完此步骤后不要再有任何SQL操作
    ```mysql
    show master status;
    
    # 记住File和Position的内容，后面配置slave要用到
	```
    ![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163658784.png)

**配置从库Slave**：

1.  修改MySQL的配置文件：`/etc/my.cnf`
    ```
    # 配置从服务器的唯一ID  
    server-id=101  
    
    #加上以下参数可以避免更新不及时，SLAVE 重启后导致的主从复制出错。  
    read_only = 1  
    master_info_repository=TABLE  
    relay_log_info_repository=TABLE
	```

2.  重启MySQL服务
    ```
    systemctl restart mysqld
	```

3.  登陆MySQL，在Slave中添加Master的信息
    ```
    # master_host是Master的ip
	# master_log_file和master_log_pos是之前查看Master状态时显示的File和Position信息  
    change master to master_host='192.168.131.100', master_user='backup', master_password='backup',master_log_file='mysql-bin.000002', master_log_pos=154;  
     ​  
    # 若出现问题  
    stop slave;
    start slave;
	```

4.  最后查看Slave的状态
    ```mysql
    show slave status\G;
	```