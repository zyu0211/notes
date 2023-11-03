---
title: Pytorch开发环境搭建
description: 记录在 win11 和 Ubuntu22.04 上搭建深度学习开发环境
tags:
  - pytorch
Author: Yuzh
data: 2022-12-15
---

## 版本选择

- miniconda：Miniconda3-py39_22.11.1-1-Linux-x86_64
- python = 3.9 
- pytorch = 1.12.1 
- torchvision = 0.13.1 
- torchaudio = 0.12.1 
- cudatoolkit = 11.6 
- cudnn = 8.7.0 for CUDA 11.x

## Minconda安装

下载地址：[https://docs.conda.io/en/latest/miniconda.html](https://docs.conda.io/en/latest/miniconda.html)

>安装

**Windows下安装**（win11）：

下载对应的 exe 文件，双击安装即可。

**Linux下安装**（Ubuntu22.04）：

```bash
# 安装
bash Miniconda3-latest-Linux-x86_64.sh

# 卸载
rm -rf ~/miniconda
rm -rf ~/.condarc ~/.conda ~/.continuum
```

>新建python环境

```bash
conda create -y -n env_name python=3.9
```

>镜像配置

清华镜像：[https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

Linux系统可以通过修改用户目录下的 `.condarc` 文件来使用清华镜像源。

Windows 用户无法直接创建名为 `.condarc` 的文件，可先执行 `conda config --set show_channel_urls yes` 生成该文件之后再修改。

修改内容如下：
```
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

修改完成后，运行 `conda clean -i` 清除索引缓存，保证用的是镜像站提供的索引。

## pytorch安装

官网地址：[https://pytorch.org/](https://pytorch.org/)

```bash
conda install pytorch==1.12.1 torchvision==0.13.1 torchaudio==0.12.1 cudatoolkit=11.6 -c pytorch -c conda-forge
```

其他版本参照官网。

## Cuda安装

>Windows下安装

**安装cuda**

下载地址：[https://developer.nvidia.com/cuda-toolkit-archive](https://developer.nvidia.com/cuda-toolkit-archive)
安装指导：[CUDA Installation Guide for Microsoft Windows (nvidia.com)](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html)

Windows下是图形安装方式，通过执行 CUDA 安装程序并按照屏幕上的提示安装 CUDA 软件。

**安装cudnn**

下载地址：[https://developer.nvidia.com/rdp/cudnn-download](https://developer.nvidia.com/rdp/cudnn-download)
安装指导：[Installation Guide :: NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#install-windows)

>Linux下安装

**安装cuda**

```bash
wget https://developer.download.nvidia.com/compute/cuda/11.6.2/local_installers/cuda_11.6.2_510.47.03_linux.runsudo

sh cuda_11.6.2_510.47.03_linux.run

# 环境变量设置
export PATH=/usr/local/cuda-11.6/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.6/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

**安装cudnn**

下载链接：[https://developer.nvidia.com/rdp/cudnn-download](https://developer.nvidia.com/rdp/cudnn-download) 
安装指导：[Installation Guide :: NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html#install-linux)
tar包安装方式如下：
```bash
# Unzip the cuDNN package.
tar -xvf cudnn-linux-x86_64-8.x.x.x_cudaX.Y-archive.tar.xz

# Copy the following files into the CUDA toolkit directory.
sudo cp cudnn-*-archive/include/cudnn*.h /usr/local/cuda/include 
sudo cp -P cudnn-*-archive/lib/libcudnn* /usr/local/cuda/lib64 
sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

## 常用库安装

- numpy
- Pillow
- matplotlib
- opencv-python
- tqdm
- scipy
- scikit-learn
- einops

命令：
```pip
pip install <包名==版本号> -i <指定镜像地址>
```

推荐镜像源
```
清华大学：https://pypi.tuna.tsinghua.edu.cn/simple
阿里云：https://mirrors.aliyun.com/pypi/simple
豆瓣：https://pypi.douban.com/simple
```