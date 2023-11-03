---
title: desktop文件简介
description: linux系统下使用desktop文件添加软件启动项
tags:
  - linux
Author: Yuzh
data: 2023-09-09
---


linux的 `.desktop` 文件是一种菜单和快捷方式的描述性文件，以下是该文件的格式及常用属性：
```
[Desktop Entry]  
//文件头

Encoding=UTF-8    
//编码方式

Name=aMule    
Name[en]=en_name  
Name[en_US]=US_name   
//应用程序名称，根据当前系统语言匹配显示，优先匹配更细化的语言标识名称

Comment=comment    
//鼠标经过上面时的提示名称，也可国际化

Exec=command    
//菜单执行的命令或程序

Icon=iconpath    
//显示在菜单项中的图标，可以为空

Terminal=false    
//是否使用终端

Type=Application   
//分类

Hidden=false  
//菜单是否隐藏，类似NoDisplay属性

Categories=Application;Network;  
//菜单所属类别，可以确定该菜单的位置
```

`.desktop` 文件所在位置：
1. `/usr/share/applications/`
2. `~/.local/share/applications/`