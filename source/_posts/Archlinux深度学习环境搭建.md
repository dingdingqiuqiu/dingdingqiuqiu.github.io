---
title: Archlinux深度学习环境搭建
date: 2024-03-07 19:18:30
tags:
categories: 
- 环境搭建
- 开发环境
---

本文主要介绍了Archlinux下本地深度学习环境的搭建,搭建新环境时，一定要查下文末的表格，找到对应的`tensorflow`版本，这个及其容易出错。用服务器的RTX4090都下不来了最新(只能cuda12.4+tensorflow2.17)不能下2.18。

<!--more-->

参考文章：[Arch Linux 配置 -- 驱动和软件安装](https://xland.cyou/p/arch-linux-configuration-driver-and-software/)

### 带深度学习环境的服务器配置

查看所有虚拟环境名

```bash
conda info --envs
```

![image-20241101081825095](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VaYjR4YkxyZmpoR3BxdXUyUkt6cnVBQkVVTF9TSGlJMENkMHhRdVctbnJzU0E_ZT1jVUJCYXA.png)

conda初始化

```bash
conda init bash
```

![image-20241101081927412](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VWUjlQcWtKZkhoR3ZuWi13d1ZYc29FQkctUkVlcVVzUzVuME5OSER3RjVBRWc_ZT1qVDZtMDI.png)

重进`shell`后激活

```bash
conda activate tensorflow
```

![image-20241101082303178](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VlTnVWckprN1FkTmpmQjVoNDR3Zm4wQmFHSDRPNFJrd2NCcFdZSWRfelFHQmc_ZT1iN2I3NjY.png)

即可执行相应深度学习训练任务

![image-20241101082648052](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VkZHViam9TNzhCT2dsQTliS2dfdWpFQkVjX1dOb0VvUW5FTWpEVEw3SzVHSnc_ZT1xSDVPZXE.png)

### wsl环境配置

nvidia-smi:查看当前版本

cuda

https://developer.nvidia.com/cuda-toolkit-archive

cudnn

https://developer.nvidia.com/cudnn-archive

```bash
sudo dpkg -i cudnn-local-repo-ubuntu2204-8.9.7.29_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-8.9.7/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudnn-cuda-12
```

tensorflow(版本对照):

https://tensorflow.google.cn/install/source

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

更新时需要重新生成`mkinitcpio`，自动更新脚本配置如下

```zsh
vim /etc/pacman.d/hooks/nvidia.hook
```

```zsh
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia-open-dkms
#Target=linux
# Change the linux part above and in the Exec line if a different kernel is used
# 如果使用不同的内核，请更改上面的 linux 部分和 Exec 行中的内容

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
#NeedsTargets
#Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
Exec=/usr/bin/mkinitcpio -P
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

> nvidia独显的话，基本只能用x11桌面
>
> optimus软件都打不开,目前在用独显模式，还需要把bios改成独显
>
> 不然桌面系统都进不去

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

### cuda&&cudnn

> 考虑到tensorflow需要对应版本的cuda和cudnn推荐google搜索官网版本下载
>
> 查询网站：
>
> https://tensorflow.google.cn/install/source
>
> 我的版本：
>
> cuda 12.4
>
> cudnn  8.9.7.29
>
> tensorflow 12.7
>
> 兼容性不太行，但可以用，有点警告，pytorch可以完全兼容
>
> 警告修复：
> https://github.com/tensorflow/tensorflow/issues/62075
>
> 中文翻译：
> https://dingdingqiuqiu.github.io/2024/09/12/tensorflow%E4%BD%BF%E7%94%A8%E8%AD%A6%E5%91%8A/#more

```zsh
yay -S cuda
```

```
yay -S cudnn
```

```zsh
cd 
vim .zshrc
```

```zsh
#cuda-setting
export PATH="/opt/cuda/bin:$PATH"
export LD_LIBRARY_PATH="/opt/cuda/lib64:$LD_LIBRARY_PATH"
export CUDA_HOME=$CUDA_HOME:/opt/cuda
export CUDA_VISIBLE_DEVICES=0
```

`export CUDA_VISIBLE_DEVICES=0`如果有四块显卡，改为`export CUDA_VISIBLE_DEVICES=0,1,2,3`

```zsh
reboot
```

> 如果不重启电脑，依然用不了，提示环境错误，版本不适配
>
> `CUDA initialization: CUDA unknown error - this may be due to an incorrectly set up environment`

测试cuda是否可用：

**torch框架**

```zsh
test.py
```

```python
import torch

print('CUDA版本:', torch.version.cuda)
print('Pytorch版本:', torch.__version__)
print('显卡是否可用:', '可用' if torch.cuda.is_available() else '不可用')
print('显卡数量:', torch.cuda.device_count())
print('是否支持BF16数字格式:', '支持' if torch.cuda.is_bf16_supported() else '不支持')
print('当前显卡型号:', torch.cuda.get_device_name())
print('当前显卡的CUDA算力:', torch.cuda.get_device_capability())
print('当前显卡的总显存:', torch.cuda.get_device_properties(0).total_memory / 1024 / 1024 / 1024, 'GB')
print('是否支持TensorCore:', '支持' if torch.cuda.get_device_properties(0).major >= 7 else '不支持')
print('当前显卡的显存使用率:', torch.cuda.memory_allocated(0) / torch.cuda.get_device_properties(0).total_memory * 100, '%')
```

```
# 结果
CUDA版本: 12.1
Pytorch版本: 2.2.2+cu121
显卡是否可用: 可用
显卡数量: 1
是否支持BF16数字格式: 支持
当前显卡型号: NVIDIA GeForce RTX 3050 Laptop GPU
当前显卡的CUDA算力: (8, 6)
当前显卡的总显存: 3.58929443359375 GB
是否支持TensorCore: 支持
当前显卡的显存使用率: 0.0 %
```

**paddle框架**

```
test.py
```

```python
import paddle
import torch
print('CUDA版本:', torch.version.cuda)
torch.cuda.is_available()
paddle.utils.run_check()
```

```
# 结果
CUDA版本: 12.1
Running verify PaddlePaddle program ... 
I0407 17:14:43.856997  8841 program_interpreter.cc:212] New Executor is Running.
W0407 17:14:43.862674  8841 gpu_resources.cc:119] Please NOTE: device: 0, GPU Compute Capability: 8.6, Driver API Version: 12.4, Runtime API Version: 11.8
W0407 17:14:43.863399  8841 gpu_resources.cc:164] device: 0, cuDNN Version: 8.9.
I0407 17:14:44.297957  8841 interpreter_util.cc:624] Standalone Executor is Used.
PaddlePaddle works well on 1 GPU.
PaddlePaddle is installed successfully! Let's start deep learning with PaddlePaddle now.
```

### python虚拟环境

> 由于系统`python`包管理属于`pacman`,有很多`pip`包无法安装
>
> 所以我们通过`python`虚拟环境的方式来安装这些包	
>
> 虚拟环境管理有很多方式，包括`conda`等，`python -m venv`更轻量级，`conda`适合大型项目

注意：

- 想要使用`cuda&&cudnn`进行深度学习，务必启动虚拟环境

  >这是因为全局环境中的`torch.__version__`为`2.2.2`
  >
  >而虚拟环境中的为`2.2.2+cu121`
  >
  >`cu121`表示 PyTorch 版本是专门针对 CUDA 12.1 编译的。
  >
  >这是由于我的`torch`是虚拟环境启动后通过`pip`安装的
  >
  >而系统上的是`pacman`安装的，版本不同

- 虚拟环境启动后，`pip`安装的包都会存在于虚拟环境中

#### 虚拟环境创建

```zsh
cd /home/P4yl04d/Documents/HENU_overview
```

```zsh
python3 -m venv myenv
```

> 这里的 `myenv` 是你想要创建的虚拟环境的名称，你可以将其替换为你喜欢的任何名称。

#### 虚拟环境激活

```zsh
source myenv/bin/activate
```

#### 退出虚拟环境

```zsh
deactivate
```

### tensorflow安装

以下查询版本对应，务必下载对应版本的cuda和cudnn

https://tensorflow.google.cn/install/source

```py
import tensorflow as tf
def test_tensorflow():
    # 打印 TensorFlow 版本
    print("TensorFlow version:", tf.__version__)
    
    # 检查是否有可用的 GPU
    gpus = tf.config.list_physical_devices('GPU')
    if gpus:
        print(f"Num GPUs Available: {len(gpus)}")
        for i, gpu in enumerate(gpus):
            print(f"GPU {i}: {gpu}")
    else:
        print("No GPUs available.")

    # 打印一些其他配置
    print("Num CPUs Available:", tf.config.list_physical_devices('CPU'))
    # 查看 CUDA 版本
    cuda_version = tf.sysconfig.get_build_info()['cuda_version']
    print("CUDA version:", cuda_version)

    # 查看 cuDNN 版本
    cudnn_version = tf.sysconfig.get_build_info()['cudnn_version']
    # 测试一个简单的运算
    try:
        a = tf.constant([[1.0, 2.0], [3.0, 4.0]])
        b = tf.constant([[5.0, 6.0], [7.0, 8.0]])
        c = tf.matmul(a, b)
        print("TensorFlow computation result (a @ b):")
        print(c.numpy())
    except Exception as e:
        print("Error during computation:", e)

if __name__ == "__main__":
    test_tensorflow()
```

