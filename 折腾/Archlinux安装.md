---
title: ArchLinux安装教程
description: 记录在实体机安装ArchLinux系统的过程
tags:
  - arch
  - linux
Author: Yuzh
data: 2023-01-15
---

## 1、启动U盘制作

1. 下载最新的ISO镜像文件，地址：[https://archlinux.org/download/](https://archlinux.org/download/)
2. 使用Ventoy制作U盘启动盘，地址：[https://www.ventoy.net/cn/download.html](https://www.ventoy.net/cn/download.html)
3. 将下载好的镜像拷贝到启动盘中。

## 2、安装前准备

1. 关闭BIOS的安全启动（Secure Boot）功能。
2. 将U盘启动设置为第一位。
3. 进入Archlinux的live安装环境

>安装教程：[https://wiki.archlinux.org/title/installation_guide](https://wiki.archlinux.org/title/installation_guide)

## 3、Live 环境下的安装

>命令行安装过于繁琐，官方提供安装脚本 archinstall 的简易安装方式。详情可参见：[https://wiki.archlinuxcn.org/wiki/Archinstall](https://wiki.archlinuxcn.org/wiki/Archinstall)

### 3.1、连接到互联网

有线以太网 —— 连接网线即可。

WiFi —— 使用 `iwctl` 验证无线网络。

```sh
device list                                #列出所有 WiFi 设备
device [网卡名字] set-property Powered on   #打开网卡如果关闭的话
adapter [网卡名字] set-property Powered on  #打开网卡如果关闭的话
station [网卡名字] scan                     #扫描网络
station [网卡名字] get-networks             #显示所有网络
station [网卡名字] connect [wifi名称]        #连接网络
```

测试网络：

```sh
ping www.baidu.com
```

### 3.2、验证引导模式

检验是否是UEFI64位模式：
```sh
cat /sys/firmware/efi/fw_platform_size
```

### 3.3、更新系统时间

```sh
timedatectl
```

### 3.4、创建硬盘分区

查看分区：

```sh
# 列出所有分区
lsblk
# 或
fidsk -l

# nvme类型：/dev/nvme0n1
# sata类型: /dev/sda1
```

创建硬盘分区：

```sh
cfdisk /dev/nvme0n1 (要被分区的磁盘)
```

分区方案 (UEFI 与 GPT 分区表的磁盘分区方案)：

|   挂载点    |            分区             |        分区类型         |   建议大小    |
| :---------: | :-------------------------: | :---------------------: | :-----------: |
| `/mnt/boot` | `/dev/efi_system_partition` |      EFI 系统分区       | 至少 300 MB |
|  `[SWAP]`   |    `/dev/swap_partition`    |  Linux swap (交换空间)  |     16GB      |
|   `/mnt`    |    `/dev/root_partition`    | Linux x86-64 根目录 (/) |     200GB     |

### 3.5、格式化分区

格式化根分区：

```sh
mkfs.ext4 /dev/root_partition (根分区)
```

初始化交换分区：

```sh
 mkswap /dev/swap_partition (交换空间分区)
```

格式化EFI系统分区：

```sh
mkfs.fat -F 32 /dev/efi_system_partition (EFI 系统分区)
```

### 3.6、挂载分区

根分区挂载到 `/mnt`：

```sh
mount /dev/root_partition (根分区) /mnt
```

挂载 EFI 系统分区：

```sh
mount --mkdir /dev/efi_system_partition (EFI 系统分区) /mnt/boot
```

启用交换分区：

```sh
swapon /dev/swap_partition (交换空间分区)
```

### 3.7、安装系统

安装之前要先更换软件镜像源：

```sh
nano /etc/pacman.d/mirrorlist

# 在第一行加入[清华源或中科大源]：
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```

刷新软件包列表：

```sh
pacman -Syy
```

重新安装 archlinux-keyring 包：

```sh
pacman -S archlinux-keyring
```

**安装 Arch 基本系统**：

```sh
pacstrap -K /mnt base base-devel linux linux-firmware linux-headers grub efibootmgr networkmanager dhcpcd git neovim
```

如果有 Intel 或 AMD 的 CPU，需要另外启用微码：

- 对于 AMD 处理器 : amd-ucode 包
- 对于 Intel 处理器 : intel-ucode 包


其他可选包：
```
ntfs-3g bluez bluez-utils
```

### 3.8、生成 fstab 文件

```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

在执行完以上命令后，检查一下生成的 `/mnt/etc/fstab` 文件是否正确。

### 3.9、chroot到新系统

```sh
arch-chroot /mnt
```

## 4、新系统下的配置

### 4.1、设置时区

```sh
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc

# 会生成 /etc/adjtime 文件
```

### 4.2、区域和本地化设置

区域设置，明确规定了地域、货币、时区日期的格式、字符排列方式和其他本地化标准。

需要设置文件：`locale.gen` 与 `locale.conf`。

1. 编辑 `/etc/locale.gen`，取消 `en_US.UTF-8 UTF-8`  和 `zh_CN.UTF-8 UTF-8` 前的注释。

2. 接着执行 `locale-gen` 以生成 locale 信息：

    ```sh
    locale-gen
    ```

3. 创建 `/etc/locale.conf` 文件，并编辑设定 LANG 变量：

    ```text
    # 添加如下内容
    LANG=en_US.UTF-8
    ```

### 4.3、网络配置

设置主机名，创建 hostname 文件：

```sh
vim /etc/hostname

# 添加如下内容：
myhostname（主机名）
```

### 4.4、设置 root 密码

修改 root 用户密码：

```sh
passwd
# 输入新密码
```

创建普通用户：

```sh
useradd -m -G wheel [用户名]

# 为用户创建密码：
passwd [用户名]

# 编辑 sudoers 赋予用户 root 权限
vim /etc/sudoers

# 删除 %wheel ALL=(ALL:ALL) ALL前的 “#”
```

### 4.5、安装引导程序

如果是双系统，需要启用 os-prober (需单独安装) 发现其他操作系统。并挂载其他系统的 efi 分区。(例如 挂载到 /mnt 上)

同时需要编辑 grub 配置文件

```sh
vim /etc/default/grub

# 去掉 GRUB_DISABLE_OS_PROBER=false前的"#"
```

安装引导：

```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
```

### 4.6、重新启动计算机

输入 exit 或按 Ctrl+d 退出 chroot 环境。`umount -R /mnt` 取消挂载。然后，执行 `reboot` 重启系统。这时候不要忘记移除安装介质，然后使用 root 帐户登录到新系统。

## 5、安装后的工作

>任重道远！

### 5.1、服务与驱动

启动网络服务：

```sh
systemctl enable NetworkManager (网络服务)
systemctl enable bluetooth (蓝牙服务)
```

英伟达显卡驱动：

```sh
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils
```

触控板驱动：

```sh
sudo pacman -S xf86-input-libinput libinput
```

### 5.2、安装桌面环境(可选)

kde：

```
pacman -S xorg plasma kde-applications(完整安装，可以不选，单独安装必备软件)
pacman -S konsole kate okular filelight dolphin ark
pacman -S sddm（kde默认的显示管理器，一般默认装）
systemctl enable sddm（开机自启显示管理）
```

### 5.3、配置软件源

添加国内源：

```
sudo vim /etc/pacman.conf

# 添加：
  [archlinuxcn]
  Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch（清华源）
   或
  Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch（中科大源）
   或
  Server = https://repo.huaweicloud.com/archlinux/$repo/os/$arch（华为源）

# 开启32为库：
去掉[multilib]及下一行前的“#”

sudo pacman -Syy(刷新)
sudo pacman -S archlinuxcn-keyring（导入密钥）
```

## 6、软件安装

>部分软件因为网络原因无法下载，可以设置代理：`export https_proxy=http://127.0.0.1:7890`

### 6.1、fcitx5输入法安装

卸载fcitx4：
```
sudo pacman -Rs $(pacman -Qsq fcitx)
```

安装fcitx5：
```
sudo pacman -S fcitx5 fcitx5-configtool fcitx5-qt fcitx5-gtk fcitx5-chinese-addons fcitx5-material-color
```

配置环境变量：
```
vim
/etc/environment

# 写入以下内容：
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
```

重启即可。

补充：安装中文字体
```
sudo pacman -S adobe-source-han-sans-cn-fonts
```
### 6.2、yay 或 paru

在配置好 archlinuxcn 后，可以使用pacman 命令直接安装：
```
sudo pacman -S yay
sudo pacman -S paru
```

### 6.3、办公软件

_1.wps_
```
yay -S wps-office-cn ttf-wps-fonts libtiff5
```

_2.LibreOffice_
```
sudo pacman -S libreoffice-still libreoffice-still-zh-cn
```

_3.obsidian_
```
yay -S obsidian
```

_4.typora_
```
yay -S typora-free
```

_5.vscode_
```
yay -S visual-studio-code-bin
```

### 6.4、浏览器

_1.google浏览器_
```
yay -S google-chrome
```

_2.firefox浏览器_
```
sudo pacman -S firefox
```

_3.edge浏览器_
```
yay -S microsoft-edge-dev-bin
```

### 6.5、日常软件

_1.QQ_
```
yay -S linuxqq
```

_2.微信_
```
yay -S electronic-wechat-uos-bin
```

_3.百度网盘_
```
yay -S baidunetdisk-bin
```

_4.腾讯会议_
```
yay -S wemeet-bin
```

_5.clash_
```
yay -S clash-for-windows-bin
```

_6.picgo_
```
yay -S picgo-appimage
```

### 6.6、开发软件

_1.idea_
```bash
yay -S intellij-idea-ultimate-edition
yay -S intellij-idea-community-edition
```

手动安装过程如下：

下载链接：[https://www.jetbrains.com/idea/download/download-thanks.html?platform=linux&code=IIC](https://www.jetbrains.com/idea/download/download-thanks.html?platform=linux&code=IIC)

下载的是 tar 包，解压后，放置在指定的目录，设置desktop文件，即可使用。
```bash
tar -zxvf ideaIC-2023.2.1.tar.gz
mkdir /opt/ideaIC/
mv ./ideaIC-2023.2.1/* /opt/ideaIC/

cd /home/zyu/.local/share/applications
vim idea.desktop

# 写入如下内容：
[Desktop Entry]
Name=IntelliJ IDEA
Comment=Intelligent Java IDE
Exec=/opt/idea-IC/bin/idea.sh
Icon=/opt/idea-IC/bin/idea.svg
Terminal=false
Type=Application
Categories=Development;IDE;
```

>解决 fcitx 输入法下，输入框不跟随光标的问题：此问题的根本原因是 IDE 附带的 JBR 不正确。

安装JBR：
```bash
# 1. 安装
yay -S jbr17-imfix

# 2. 找到安装位置：/usr/lib/jvm/jbr17-imfix
sudo find / -name jbr17-imfix

# 3. 进入Idea，更换JBR，步骤如下：
IDEA 中依次选择： `Help -> Find Action -> Choose Boot Java Runtime for the IDE` 更换 jbr
```

也可以手动安装，参照：[https://github.com/RikudouPatrickstar/JetBrainsRuntime-for-Linux-x64/releases](https://github.com/RikudouPatrickstar/JetBrainsRuntime-for-Linux-x64/releases)

_2.pycharm_
```bash
yay -S pycharm-community-edition
```

手动安装过程如下：

下载链接：[https://www.jetbrains.com/pycharm/download/download-thanks.html?platform=linux&code=PCC](https://www.jetbrains.com/pycharm/download/download-thanks.html?platform=linux&code=PCC)

后续步骤和 idea 的安装类似：
```bash
tar -zxvf pycharm-community-2023.2.1.tar.gz
mkdir /opt/pycharm-community/
mv ./pycharm-community-2023.2.1/* /opt/pycharm-community/

cd /home/zyu/.local/share/applications
vim pycharm.desktop

# 写入如下内容：
[Desktop Entry]  
Terminal=false  
Type=Application  
Name=PyCharm  
Comment=The Python IDE for Professional Developers  
Exec=/opt/pycharm-community/bin/pycharm.sh  
Icon=/opt/pycharm-community/bin/pycharm.svg  
Categories=Development;IDE;
```

_3.dbeaver_
```
sudo pacman -S dbeaver
```

### 6.7、开发环境

_1.git_
```bash
sudo pacman -S git

git config --global user.name "zhangyu"
git config --global user.email zyu0211@gmail.com
```

_2.jdk_

```bash
sudo pacman -S jdk17-openjdk
sudo pacman -S jdk8-openjdk

# 查看已安装的 Java 版本
archlinux-java status

# 设置JDK8为默认
sudo archlinux-java set java-17-openjdk

# 配置 JAVA_HOME
echo 'export JAVA_HOME=/usr/lib/jvm/default' >> ~/.bashrc 
source ~/.bashrc
```

_3.maven_

```bash
sudo pacman -S maven

# Maven home: /opt/maven
```

_4.golang_

```bash
sudo pacman -S go

# 在 `~/.bashrc` 中配置:
export PATH="$PATH:$(go env GOBIN):$(go env GOPATH)/bin"
```

_5.c/cpp_

```bash
sudo pacman -S gcc
```

_5.nodejs_

```bash
# 查询长期支持版本
pacman -Ss nodejs-lts

# 安装
sudo pacman -S nodejs-lts-gallium
```

_6.miniconda_

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh

~/miniconda3/bin/conda init zsh
```

_7.python_

```bash
sudo pacman -S python
sudo pacman -S python-pip
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
