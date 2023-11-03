---
title: wsl2安装教程
description: 在 Windows11 系统安装 archlinux 发行版的 wsl，并对初始的 arch 系统进行配置
tags:
  - wsl
  - arch
Author: Yuzh
data: 2023-06-01
---

>系统：win11 22H2

## 先决条件

必须运行 Windows 10 版本 2004 及更高版本（内部版本 19041 及更高版本）或 Windows 11 才能使用以下命令。

开启 `适用于 Linux 的 Windows 子系统` 和 `虚拟机平台`，如下图所示。

![](https://cdn.jsdelivr.net/gh/zyu0211/img-repository@main/blog/image-20230725163918590.png)


## 下载与安装

Github上的[ArchWSL](https://link.zhihu.com/?target=https%3A//github.com/yuk7/ArchWSL)项目已经帮我们把 ArchLinux 集成好了，可以到下载页面下载最新的 Arch.zip 文件：[下载页面](https://link.zhihu.com/?target=https%3A//github.com/yuk7/ArchWSL/releases/latest)。

下载好之后，解压其中的文件到你需要存放 ArchLinux 的路径，例如 `D:\archlinux`，解压后会生成两个文件：`Arch.exe`、`rootfs.tar.gz`。

随后执行目录下的 Arch.exe 文件，安装程序会自动将 ArchLinux 安装到同目录下面，并配置好 wsl，安装成功后会在目录下生成 `ext4.vhdx` 虚拟磁盘映射文件。

>若安装过程中出现问题：ERR: WSL 2 requires an update to its kernel component. For information please visit https://aka.ms/wsl2kernel ，需要更新 WSL2 的 Linux 内核，打开网站，下载安装即可。

安装完成之后，打开终端，应该可以看到刚装好的ArchLinux系统：
```
$ wsl -l
适用于 Linux 的 Windows 子系统分发版:
Arch (默认)
$ wsl -l -v
  NAME    STATE           VERSION
* Arch    Stopped         2
```

## 初始设置

***启动 ArchLinux 发行版***
```
wsl -d Arch
若为默认发行版，可直接使用wsl
wsl
```

***配置pacman***

首先，配置pacman镜像源，改为国内的。
```bash
vim /etc/pacman.d/mirrorlist
```

增加以下内容：
```text
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

输入下面命令，配置pacman key：
```bash
pacman-key --init
pacman-key --populate
pacman -Syy archlinux-keyring
```

更新系统：
```bash
pacman -Syu
```

配置archlinuxcn镜像源：
```bash
vim /etc/pacman.conf
```

增加以下内容：
```bash
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

安装archlinuxcn的key：
```bash
pacman -Sy archlinuxcn-keyring
```

安装 yay
```
sudo pacman -S yay
```

***配置用户***

为 root 设置密码：
```bash
passwd
回车后，输入新密码
```

配置sudo：
```bash
echo "%wheel ALL=(ALL) ALL" > /etc/sudoers.d/wheel
```

添加非root用户：
```bash
useradd -m -G wheel -s /bin/bash {username}
passwd {username}
```

退出ArchLinux，进入刚刚安装ArchLinux的目录（例如`D:\archlinux`），将默认用户改为非 root 用户：
```bash
exit # 退出archlinux，之后你会回到Windows
cd D:\archlinux
.\Arch.exe config --default-user {username}
```

重启wsl并再次进入ArchLinux，你应该会进入非 root 用户：
```bash
wsl --shutdown  # 这个命令会关闭所有虚拟机
wsl -d Arch
```

## 安装 ZSH 并美化

下载和安装`zsh`、 `zsh`主题 、 `zsh`插件
```bash
sudo pacman -S zsh
sudo pacman -S zsh-autosuggestions
sudo pacman -S zsh-syntax-highlighting
sudo pacman -S zsh-theme-powerlevel10k
```

设置`zsh`为默认`shell`
```bash
chsh -s /usr/bin/zsh

这个需要两个用户都设置，`普通`用户和`root`用户
```

启用插件和主题
```
使用 vim 打开 `~/.zshrc`，没有可以用`touch`创建，添加下面内容

source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh-theme-powerlevel10k/powerlevel10k.zsh-theme 
```

注销或重启，就进入主题配置界面，格局提示配置完成即可。

## 开发环境搭建

**python环境**
```
sudo pacman -S python-pip

pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

**Java环境**
```
sudo pacman -S jdk8-openjdk
sudo pacman -S jdk17-openjdk

# 查看已安装的 Java 版本
archlinux-java status

# 设置JDK8为默认
sudo archlinux-java set java-8-openjdk
```

**Golang环境**
```bash
sudo pacman -S go

# 在 `~/.bashrc` 中配置:
export PATH="$PATH:$(go env GOBIN):$(go env GOPATH)/bin"
```

**cpp/c环境**
```bash
sudo pacman -S gcc
```

**git**
```bash
sudo pacman -S git

git config --global user.name "zyu0211"
git config --global user.email zyuxdu@qq.com
```

## WSL 基本命令

### 安装命令
```
wsl --install [option]

选项包括：

--distribution：指定要安装的 Linux 发行版。 
--no-launch：安装 Linux 发行版，但不自动启动它。
--web-download：通过联机渠道安装，而不是使用 Microsoft Store 安装。

未安装 WSL 时，选项包括：

--inbox：使用 Windows 组件（而不是 Microsoft Store）安装 WSL。
--enable-wsl1：在安装 Microsoft Store 版本的 WSL 的过程中也启用“适用于 Linux 的 Windows 子系统”可选组件，从而启用 WSL 1。
--no-distribution：安装 WSL 时不安装发行版。
```

### 列出可用的 Linux 发行版
```
wsl --list --online
wsl -l -o
```

### 列出已安装的 Linux 发行版
```
wsl --list --verbose
wsl -l -v

可与 list 命令一起使用的其他选项包括：`--all`（列出所有发行版）、`--running`（仅列出当前正在运行的发行版）或 `--quiet`（仅显示发行版名称）
```

### 设置 WSL 的版本
```
设置指定发行版的 WSL 版本
wsl --set-version <distribution name> <versionNumber>

设置默认的 WSL 版本
wsl --set-default-version <Version>
```

### 设置默认 Linux 发行版
```
wsl --set-default <Distribution Name>
```

### 将目录更改为主页
```
wsl ~

`~` 可与 wsl 一起使用，以在用户的主目录中启动。
```

### 指定用户运行Linux发行版
```
wsl --distribution <Distribution Name> --user <User Name>
```

### 更新 WSL
```
wsl --update
wsl --status
wsl --version
wsl --help
```

### 更改发行版的默认用户
```
<DistributionName> config --default-user <Username>

用户必须已经存在于发行版中才能成为默认用户
```

### 关闭WSL
```
立即终止所有正在运行的发行版和 WSL2 轻量级实用工具虚拟机
wsl --shutdown

终止指定的发行版或阻止其运行
wsl --terminate <Distribution Name>
```

### 导入和导出发行版
```
导出
wsl --export <Distribution Name> <FileName>

导入
wsl --import <Distribution Name> <InstallLocation> <FileName>

将指定 tar 文件导入和导出为新的发行版。选项包括：

--vhd：指定导入/导出发行版应为 .vhdx 文件，而不是 tar 文件
--version：（仅导入）指定将发行版导入为 WSL 1 还是 WSL 2 发行版

就地导入，将指定的 .vhdx 文件导入为新的发行版。虚拟硬盘必须采用 ext4 文件系统类型格式。
wsl --import-in-place <Distribution Name> <FileName>
```

### 注销或卸载 Linux 发行版
```
wsl --unregister <DistributionName>
```