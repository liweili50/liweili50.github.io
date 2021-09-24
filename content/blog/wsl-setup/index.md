---
title: 使用LxRunOffline管理WSL
date: "2021-09-24 19:12:10"
tags:
  - Linux
  - WSL
description: ""
---

WSL(适用于 Linux 的 Windows 子系统)可让开发人员直接在 Windows 上按原样运行 GNU/Linux 环境（包括大多数命令行工具、实用工具和应用程序），且不会产生传统虚拟机或双启动设置开销。

#### WSL 的安装

- 以管理员身份运行 powershell ，输入下面的代码，等待提示完成后，重启系统：

```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

- 在 win10 商店里直接搜索 Ubuntu 即可选择版本下载安装
- 第一次打开会初始化，等待安装完毕，然后设置好用户名和密码

到这里就完成了 WSL 的安装，但这其实只是 WSL1，微软更推荐使用 WSL2，他们的区别可以参考知乎讨论：
[从 wsl 到 wsl2 明显是退步，为什么还有人鼓吹 wsl2？](https://www.zhihu.com/question/424191615/answer/1842582111)

#### 接下来就是 WSL2 的安装

- 以管理员身份打开 PowerShell 并运行：

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

- 执行以下命令将 wsl1 转为 wsl2

```
wsl --set-version Ubuntu 2
```

- 查看版本

```
wsl -l -v
```

至此 WSL2 的安装也大功告成，但是通过这种安装是装在 C 盘目录下的，随着后续的使用不可避免的会导致 C 盘越来越大，那可不可以安装到别的盘呢？答案当然是可以的，而且还支持更强大的功能！

#### 使用 LxRunOffline 让 WSL 更好用

LxRunOffline 可以将任何发行版的 Linux 以 WSL 形式安装到 Windows 10 中，除此之外还可以把已安装在 C 盘的 WSL 转移到指定位置，还支持可以实现 WSL 系统备份和恢复。

LxRunOffline 的下载可以通过 GitHub 下载源文件直接安装，也可以通过包管理工具安装，比如我使用的是 choco：

```
choco install lxrunofflines
```

- 安装完毕之后运行命令查看已安装的 WSL

```
LxRunOffline l
```

正常会输出已安装的 Linux 发行版的名称，比如：Ubuntu

- 然后输入一下命令，比如我将 Ubuntu 转移到我的 D 盘下的 D:\Linux\Ubuntu 中：

```
LxRunOffline m -n Ubuntu -d D:\Linux\Ubuntu

```

- 最后查看路径看是否已经完成

```
LxRunOffline di -n Ubuntu

```

至此从 WSL1 的安装到转换到 WSL2，然后利用 LxRunOffline 移动到 D 盘都已完成，这就是我经历的整个折腾过程。

当然 LxRunOffline 还有其他很重要的功能请访问：
[A full-featured utility for managing Windows Subsystem for Linux](https://github.com/DDoSolitary/LxRunOffline)
