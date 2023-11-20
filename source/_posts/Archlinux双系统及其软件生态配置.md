---
title: Archlinux双系统及其软件生态配置
date: 2023-11-07 16:51:56
tags:
---

### 双系统安装

照着下面这个up主装就行。

https://www.bilibili.com/video/BV1fk4y1w7wq/?spm_id_from=333.999.0.0

主要记录期间遇到的几个问题

1.`iwctl`中`WIFI`连接遇到问题？

有可能是无线网络被锁住了，在进入`iwctl`前运行以下命令即可解锁

```bash
ip link
rfkill unblock wifi
```

2.一个电脑两个磁盘，一个磁盘放`Windows`,另一个磁盘放`Archlinux`是否可行？

在`Windows`磁盘管理工具中将第二个磁盘所有盘符删除，整个第二块磁盘状态为未分配，在使用`cfdisk`时新建`EIF`分区（当然与第一块磁盘中的`Windows`共用`EIF`分区同样可行），`GRUB`在开了多系统检测之后可以检测多个磁盘上的系统。注意用`mkfs`对`FIF`分区格式化即可。

```bash
mkfs.fat -F32 /dev/EIF所在磁盘区域
```

> 注：`nvme`开头是使用`NVMe`接口标准的存储设备
>
> `sda`开头是使用`SATA`接口标准的存储设备
>
> `SSD`是固态硬盘（Solid State Drive）的简称
>
> `NVMe`接口速度更快延迟更低，主要用于高性能`SSD`
>
> `SATA`接口标准主要用于机械硬盘和`SSD`

### 软件生态配置

`Archlinux`中下载软件推荐上[Archwiki](https://wiki.archlinux.org/)（输入软件名即可，内有中文社区，点`AUR`中也可）

```bash
sudo pacman -S 软件包名（官方版）
```

```bash
yay -S 软件包名（AUR包）
```

自己下载解压安装亦可，初始环境配置推荐下面这篇文章

https://zhuanlan.zhihu.com/p/617640635



