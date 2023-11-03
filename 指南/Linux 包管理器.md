---
title: Linux包管理器介绍
description: 介绍各种 Linux 发行版上的包管理器的基本使用方法
tags:
  - linux
Author: Yuzh
data: 2023-06-06
---

## DPKG

>Debian 包管理系统 Debian Package Management Syste。Dpkg 是 Debian Linux 家族的基础包管理系统，它用于安装、删除、存储和提供`.deb`包的信息。

**安装包**
```
dpkg -i xxx.deb

# 递归地，安装在指定目录及其所有子目录中所有匹配 “*.deb” 的常规文件
dpkg -R --install debpackages/
```

**列出所有已安装的包**
```
dpkg -l

# 要查看已安装或未安装的特定包，请使用选项 “-l” 和 包名
dpkg -l package-name
```

**删除包**
```
dpkg -r package-name
dpkg -p package-name

# 使用 “p” 选项代替 “r”，这将连同配置文件一起删除包。“r” 选项将只删除包而不是配置文件。
```

**查看特定包内容**
```
dpkg -c xxx.deb
```

**检查包是否安装**
```
dpkg -s package-name
<--status>
```

**检查安装包的位置**
```
dpkg -L package-name
```

**解压包**
```
# 解压包，但不会安装或配置它
dpkg --unpack xxx.deb

# 重新配置一个已经解压的包
dpkg --configure xxx
```

**包可用信息**
```
# 用 Packages 文件中的可用信息替换旧信息
dpkg --update-avail package_name

# 删除有关哪些包可用的当前信息
dpkg –-clear-avail

# 自动忘记已卸载和不可用的包
dpkg --forget-old-unavail
```

### APT

>高级打包工具 Advanced Packaging Tool。这个是一个 dpkg 包管理系统的前端工具，它是一个非常受欢迎的、自由而强大的，有用的命令行包管理器系统。

**安装包**
```
apt install package-name

# 安装 .deb 包
apt deb xxx.deb
```

**查找已安装软件包的位置**
```
apt content package-name
```

**检查已安装的软件包版本**
```
apt version package-name
```

**检查包的所有依赖项**
```
apt depends package-name
```

**搜索包**
```
apt search package-name
```

**查看有关包的信息**
```
apt show package-name
```

**验证包是否存在任何损坏的依赖项**
```
apt check package-name
```

**列出给定包的推荐缺失包**
```
apt recommends package-name
```

**更新系统软件包**
```
apt update
apt upgrade
```

**删除包**
```
# 删除未使用的包
apt autoremove

# 清理下载包的旧存储库
apt autoclean

# 删除包及其配置文件
apt purge package-name
```

## RPM

> 红帽包管理器 Red Hat Package Manager。这个是红帽创建的 Linux 基本标准（LSB）打包格式和基础包管理系统，基于这个底层系统，有多个前端包管理工具可供使用。

```
# 查询已安装的 rpm 包列表
rpm -qa | grep xxx 

# 查询软件包是否安装
rpm -q xxx 
    -qi：查询软件包的信息；
    -ql：查询软件包中的文件

# 查询文件所属的软件包
rpm -qf 文件全路径名 

# 卸载 rpm 包
rpm -e rpm包名 
    --nodeps 强制删除

# 安装 rpm 包
rpm -ivh rpm包的全路径名 
    -i ：install安装；
    -v：verbose详细信息；
    -h：hash进度条
```

### YUM

>黄狗更新器，修改版Yellowdog Updater, Modified。这个是一个开源、流行的命令行包管理器，它是用户使用 RPM 的界面（之一）。你可以把它和 Debian Linux 系统中的 APT 进行对比，它和 APT 拥有相同的功能。

**安装软件包**
```
yum install package-name
```

**删除包**
```
yum remove package-name

# 清理 yum 缓存
yum clean all
```

**更新软件包**
```
# 更新指定包
yum update package-name

# 检查可用更新
yum check-update

# 更新系统
yum update
```

**搜索软件包**
```
# 搜索具有名称的特定包
yum list package-name

# 列出 Yum 数据库中所有可用的软件包
yum list | less

# 列出所有已安装的软件包
yum list installed | less

# 如果不记得包的确切名称，请使用搜索功能搜索所有可用的包，以匹配您指定的包的名称
yum search part-name
```

**获取包信息**
```
yum info package-name
```

**查看指定文件属于那个包**
```
# 查看 /etc/httpd/conf/httpd.conf 文件属于哪个包
yum provides /etc/httpd/conf/httpd.conf
```

**列出已启用的 yum 存储库**
```
yum repolist

# 指定仓库安装
yum --enablerepo=repository-name install package-name
```

## Pacman

>这个是一个流行的、强大而易用的包管理器，它用于 Arch Linux 和其他的一些小众发行版。它提供了一些其他包管理器提供的基本功能，包括安装、自动解决依赖关系、升级、卸载和降级软件。

**安装指定的包**
```
# 安装单个或者一系列软件包
pacman -S 包名_1 包名_2 ...

# 通过正则表达式安装一系列软件包
pacman -S $(pacman -Ssq 包正则表达式)

# 要安装多个含有相似名称的软件包，可以使用花括号扩展
pacman -S plasma-{desktop,mediacenter,nm}
```

**删除软件包**
```
# 删除单个软件包，但保留其全部已经安装的依赖关系
pacman -R package-name

# 删除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系
pacman -Rs package-name
pacman -Rsu package-name

# 删除软件包和所有依赖这个软件包的程序
pacman -Rsc package-name

# 要删除一个被其他软件包依赖的软件包，但是不删除依赖这个软件包的其他软件包
pacman -Rdd package-name

# pacman删除某些程序时会备份重要配置文件，在其后面加上.pacsave扩展名。避免备份这些文件
pacman -Rn package-name

# 删除目前没有安装的所有缓存的包，和没有被使用的同步数据库
pacman -Sc

# 要删除缓存中的全部文件
pacman -Scc
```

**升级软件包**
```
pacman -Syu

-Sy：这个选项用于更新本地软件包数据库。它会检查远程服务器上的软件包数据库，并将本地数据库与之同步。这个选项通常在安装新软件包之前执行，以确保你使用的软件包列表是最新的。

-Syy：与 -Sy 类似，但它会强制刷新本地软件包数据库。即使你的本地数据库已经是最新的，这个选项也会重新下载软件包数据库。这个选项很少需要使用，除非你怀疑本地数据库已损坏或过时。

-Syu：这个选项用于更新系统中的所有已安装软件包，并升级到最新版本。它会检查远程服务器上的软件包数据库，查找可用的软件包更新，并下载安装这些更新。这个选项是更新整个系统的推荐方式。

-Su：与 -Syu 类似，但它只升级已安装的软件包，而不检查远程服务器上的软件包数据库。这个选项可以在你已经知道没有可用更新时使用，以节省一些时间和网络流量。
```

**查询包数据库**

pacman 使用 `-Q` 参数查询本地软件包数据库， `-S` 查询同步数据库，以及 `-F`查询文件数据库。
```
# pacman 可以在包数据库中查询软件包，查询位置包含了软件包的名字和描述：
pacman -Ss string1 string2 ...

# 有时，`-s`的内置正则会匹配很多不需要的结果，所以应当指定仅搜索包名，而非描述或其他子段:
pacman -Ss '^vim-'

# 要查询已安装的软件包：
pacman -Qs string1 string2 ...

# 按文件名查找软件库：
pacman -F string1 string2 ...

# 显示软件包的详尽的信息：
pacman -Si package-name

# 查询本地安装包的详细信息：
pacman -Qi package-name

# 使用两个 `-i` 将同时显示备份文件和修改状态：
pacman -Qii package-name

# 要获取已安装软件包所包含文件的列表：
pacman -Ql package-name

# 查询远程库中软件包包含的文件：
pacman -Fl package-name

# 检查软件包安装的文件是否都存在（两个参数`k`将会执行一次更彻底的检查）：
pacman -Qk package-name

# 查询数据库获取某个文件属于哪个软件包：
pacman -Qo /path/to/file-name

# 查询文件属于远程数据库中的哪个软件包：
pacman -F /path/to/file-name

# 要罗列所有不再作为依赖的软件包(孤立orphans)：
pacman -Qdt

# 要罗列所有明确安装而且不被其它包依赖的软件包：
 pacman -Qet
```

**Pactree**
```
显示软件包的依赖树
pactree package-name
```

**清理软件包缓存**

_pacman_ 将下载的软件包保存在 `/var/cache/pacman/pkg/` 并且不会自动移除旧的和未安装版本的软件包。需要定期手动清理缓存来避免该文件夹无限制增大。
```
# 删除所有缓存的版本和已卸载的软件包，除了最近的 3 个会被保留
paccache -r

# 自己设置保留最近几个版本
paccache -rk1

# 添加`-u`/`--uninstalled`开关来限制 paccache 的行为只作用于卸载的包。
paccache -ruk0
```

**其他命令**
```
# 下载包而不安装
pacman -Sw package-name

# 安装一个本地包(不从源里下载）
pacman -U /path/to/package/package-name-version.pkg.tar.zst

# 将本地包保存至缓存
pacman -U file:///path/to/package/package-name-version.pkg.tar.zst

# 安装一个远程包（不在 pacman 配置的源里面）
pacman -U http://www.example.com/repo/example.pkg.tar.zst

```