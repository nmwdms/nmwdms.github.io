---
title: （docker初探）win10安装docker教程
tags: 学习新知识
---

本文介绍如何在win10上进行docker的安装，以及安装过程中遇到问题的解决方法

**1.win10上安装docker**
参考文章如下：
- [Windows Docker 安装](https://www.runoob.com/docker/windows-docker-install.html)


**2.遇到的问题**
***1.WSL 2安装失败***
参考如下链接进行手动安装即可
- [下载 Linux 内核更新包](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)
***2.Failed to set version to docker-desktop: exit code: -1***
需要下载NoLsp.exe，并用管理员身份运行cmd，指令如下
NoLsp.exe c:\windows\system32\wsl.exe
- [NoLsp.exe下载链接]](www.proxifier.com/tmp/Test20200228/NoLsp.exe)
- [解决方法](https://github.com/microsoft/WSL/issues/4177#issuecomment-597736482)
