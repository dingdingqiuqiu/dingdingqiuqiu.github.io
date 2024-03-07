---
title: Archlinux深度学习环境搭建
date: 2024-03-07 19:18:30
tags:
categories: 
- 环境搭建
- 开发环境
---

本文主要介绍了Archlinux下本地深度学习环境的搭建

<!--more-->

参考文章：[Arch Linux 配置 -- 驱动和软件安装](https://xland.cyou/p/arch-linux-configuration-driver-and-software/)

### 显卡安装

```bash
sudo pacman -S nvidia-open-dkms nvidia-settings lib32-nvidia-utils # 必须安装
```

安装好驱动后，编辑`/etc/mkinitcpio.conf`，删去`HOOKS`那一项中得`kms`，阻止内核启动时加载`nouveau`。

```zsh
# HOOKS=(base udev autodetect modconf keyboard keymap kms consolefont block filesystems fsck)  # 更改前
HOOKS=(base udev autodetect modconf keyboard keymap consolefont block filesystems fsck)  # 更改后
```

更改后重新生成`initramfs`

```zsh
sudo mkinitcpio -P
```

重启电脑后，键入下列代码，回显如下

```zsh
lspci -k | grep -A 2 -E "(VGA|3D)"
01:00.0 VGA compatible controller: NVIDIA Corporation GA107BM [GeForce RTX 3050 Mobile] (rev a1)
        Subsystem: Lenovo GA107BM [GeForce RTX 3050 Mobile]
        Kernel driver in use: nvidia
--
06:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Cezanne [Radeon Vega Series / Radeon Vega Mobile Series] (rev c5)
        Subsystem: Lenovo Cezanne [Radeon Vega Series / Radeon Vega Mobile Series]
        Kernel driver in use: amdgpu
```

### 核显和独显动态切换

```zsh
yay -S optimus-manager optimus-manager-qt
```

```zsh
sudo vim /etc/environment
```

```bash
__NV_PRIME_RENDER_OFFLOAD=1
__GLX_VENDOR_LIBRARY_NAME="nvidia"
__VK_LAYER_NV_optimus="NVIDIA_only"
```

