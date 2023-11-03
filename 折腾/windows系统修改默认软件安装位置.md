---
title: windows修改软件默认安装位置
description: windows系统安装某些软件不能自定义安装路径，而是会默认安装，下面介绍了怎么修改默认软件安装位置
tags:
  - windows
Author: Yuzh
data: 2022-10-01
---

## 修改windows系统的默认软件安装位置

### 问题

某些软件会默认安装到c盘，如何更改安装盘符释放c盘空间呢？
### 原理
windows系统的默认软件安装位置：一般有 `ProgramFilesDir` 和 `ProgramFilesDir(x86)`
### 解决
1. win+r，输入 `regedit` 打开注册表编辑器
2. 在注册表中，找到如下路径：`\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion`
3. 在上述目录下有两个路径配置： `ProgramFilesDir` 和 `ProgramFilesDir(x86)`，所以要修改此配置项。
4. 默认值分别是：`C:\Program Files`和 `C:\Program Files (x86)`，修改为想要安装的路径即可。
5. 安装完成后，再改回默认值，否则有些程序调用注册表，会报错误。

