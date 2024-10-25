---
title: Archlinux双系统及其软件生态配置
date: 2023-11-07 16:51:56
tags: 
- 双系统安装
- clash
- pycharm
- vim
- zsh
- wine
categories: 
- 环境搭建
- 开发环境
---
本文主要记录了双系统安装的过程以及clash,pycharm,vim,zsh等软件相关安装配置
<!--more-->

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

3.对自用用户赋权root组，不然权限好低，一点也不方便

```zsh
gpasswd -a P4yl04d root    
```

#### 启动盘制作及分区

`iso`镜像下载

```
https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/
```

启动盘制作工具下载


```
https://ventoy.net/cn/download.html
```

`ventoy`下载解压后，打开`Ventory2Disk`，设置分区类型为`GPT`(这里可供选择的有`MBR`和`GPT`,详细介绍在《鸟哥的linux私房菜》P131)

> 关于分区各种格式的详细介绍可以参考这篇[电脑是怎么开机的](https://dingdingqiuqiu.github.io/2024/10/08/%E7%94%B5%E8%84%91%E6%98%AF%E6%80%8E%E4%B9%88%E5%BC%80%E6%9C%BA%E7%9A%84(x86)/#more)

> MBR: 早期的，最大2.2TB,开头扇区记录分区信息及开机启动项，且开机管理程序区块仅446Bytes,较小
>
> GPT: 新兴的，补充下，fdisk工具目前支持gpt分区的识别（Linux和Windows磁盘均使用的gpt），使用grub引导似乎也没啥问题
>
> 相关知识速览: https://segmentfault.com/a/1190000020850901

分区文件系统类型为`exFAt`即可(后面反正要重新格式化，其实无所凋萎)，分区4kb对齐，簇大小默认。

点击安装，即可格式化原USB,拖入下载的`iso`镜像文件,完成启动盘制作。

使用windos里的磁盘管理工具可查看磁盘状态，未分配的都是可用的，删除卷，压缩卷均可腾出未分配空间

启动前记得去`UEFI/BIOS`里关闭安全启动选项

#### 启动盘启动

插上USB,进入`UEFI/BIOS`里，选择`从USB启动`。

页面一:选择要安装的镜像后回车(ventory支持多镜像启动盘)。

页面二:选择`Boot in normal mode`（第一个选项），即grub引导方式安装。

页面三:进入`grub`页面，选择`Archlinux install...`（第一个选项）即可。

> 由于archlinux安装在命令行下，live环境中，所以我们后面更换主板找回grub时，不用像别的系统一样还要找`try un** without install`的选项。

#### WIFI连接

解锁`wifi`

```bash
ip link
```

```bash
rfkill unblock wifi
```

使用`iwctl`连接（`wlan0`是我的网卡设备名，`Link`是我的`WIFI`名）

```zsh
iwctl
```

```bash
device list
```

```bash
station wlan0 scan
```

```bash
station wlan0 get-networks
```

```bash
station wlan0 connect Link
```

输入密码，ctrl+d退出`iwctl`后，检测连接

```zsh
ping baidu.com
```

#### 分区及安装系统

更新系统时间

```bash
timedatectl
```

查看分区（图片这里是分好后的效果）

```bash
fdisk -l
```

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7m0DYiq30zod2xxZG?embed=1&width=1660&height=1648" width="1660" height="" />

如图`/dev/nvme0n1`开头是`windows`下的，不用动;我们所有操作都在第二块固态上，即`/dev/nvme1n1`开头

进入操作页面

```bash
cfdisk dev/nvme1n1
```

可以看到都是未分配的，我们在上方选中`Free space`,下方选中`New`，输入文件大小，创建四个分区，分别留作efi分区，根分区，home分区，swap分区，大小建议为`0.3G`，`随便`，`随便`，`内存*2G`

然后分别在上方选中四个分区，下方选中`Type`,分区类型为分别为`EFI System`,`Linux filename `,`Linux home`,`Linux swap`。

执行分区类型写入，分别在上方选中四个分区，下方选中`Write`，分别对四个分区写入。

写入后，下方选中`Quit`退出即可。

再次执行`fdisk -l`，检查分区情况，没问题就可以继续了，后面我们进行格式化分区。

格式化`EFI分区`(位置在`/dev/nvme1n1p2`)

```
mkfs.fat -F 32 /dev/nvme1n1p2
```

> 创建了一个FAT32文件系统，等价命令为`mkfs.vfat /dev/nvme1n1p2`

格式化`根分区`(位置在`/dev/nvme1n1p3`)

```bash
mkfs.ext4 /dev/nvme1n1p3
```

格式化`home分区`(位置在`/dev/nvme1n1p4`)

```zsh
mkfs.ext4 /dev/nvme1n1p4
```

格式化`swap分区`(位置在`/dev/nvme1n1p5`)

```zsh
mkswap /dev/nvme1n1p5
```

挂载分区(一定要先挂载根分区)

挂载根分区（位置在`/dev/nvme1n1p3`)

```
mount /dev/nvme1n1p3 /mnt
```

挂载home分区（位置在`/dev/nvme1n1p4`）

```
mount --mkdir /dev/nvme1n1p4 /mnt/home
```

挂载efi分区(位置在`/dev/nvme1n1p2`)

```
mount --mkdir /dev/nvme1n1p2 /mnt/efi
```

挂载交换分区（位置在`/dev/nvme1n1p5`）

```
swapon /dev/nvme1n1p5
```

配置`pacman`国内源

```
nano /etc/pacman.d/mirrorlist
```

第一行添加

```
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

保存退出，刷新缓存

```
pacman -Syyu
```

重新安装密钥

```
pacman -S archlinux-keyring
```

安装基本操作系统

```
pacstrap /mnt base base-devel linux-zen linux-zen-headers linux-firmware networkmanager grub os-prober efibootmgr ntfs-3g amd-ucode bluez bluez-utils nano
```

> base：基础系统
>
> base-devel: 工具包
>
> linux-zen: 高性能内核
>
> linux-zen-headers: 高性能内核头文件
>
> linux-firmware: linux固件
>
> networkmanager: 网络
>
> grub: 引导
>
> os-prober: 多系统检测
>
> efibootmgr: efi启动项管理
>
> ntfs-3g: ntfs可读写
>
> amd-ucode: cpu编码，如果是cpu为intel的，那就intel-ucode
>
> bluez bluez-utils: 蓝牙
>
> nano: 文本编辑器

一路回车安装即可。

创建fstab（自动挂载配置文件）

```
genfstab -U /mnt >> /mnt/etc/fstab
```

`arch-chroot`进入系统

```
arch-chroot /mnt
```

设置时区

```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

设置硬件时间

```bash
hwclock --systohc
```

本地化

编辑`/etc/locale.gen`

```
nano /etc/locale.gen
```

删除`en_US.UTF-8 UTF-8`和`zh_CN.UTF-8 UTF-8`前的`#`

生成`locale`

```
locale-gen
```

编辑`/etc/locale.conf`

```
nano /etc/locale.conf
```

添加

```
LANG=en_US.UTF-8
```

保存退出

设置主机名

编辑`/etc/hostname`

```
nano /etc/hosthome
```

就叫`Arch`吧

```
Arch
```

设置`root`密码

```
passwd
```

输两次密码即可

创建普通用户`P4yl04d`

```
useradd -m -G wheel P4yl04d
```

为普通用户创建密码

```
passwd P4yl04d
```

编辑`/etc/sudoers`赋予用户`root`权限

```
nano /etc/sudoers
```

删除`%wheel ALL=(ALL:ALL) ALL`前的`#`

启动服务

网络服务

```
systemctl enable NetworkManager 
```

蓝牙服务

```
systemctl enable bluetooth
```

编辑`grub`，启用多系统检测

```
nano /etc/default/grub
```

去掉`GRUB_DISABLE_OS_PROBER=false`前的`#`

安装`grub`服务

```
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=grub
```

更新`grub`引导

```
grub-mkconfig -o /boot/grub/grub.cfg
```

#### 安装桌面环境`KDE`

基本组件

```
pacman -S xorg plasma plasma-wayland-session
```

开机自启动显示管理

```
systemctl enable sddm 
```

其他必要的东西

```
pacman -S konsole dophin ark kate
```

> konsole: 终端
>
> dophin: 文件管理器
>
> ark: 解压缩软件
>
> kate: 文本编辑器

退出系统，重启电脑即可进入`Archlinux`桌面环境工作

```
exit
```

```
reboot
```

重启后在`grub`引导中并未看到`Windows`选项，正常现象

我们进入`Arch`,重新更新下，下次就有了

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

从回显信息我们可以看到，`grub`找到了`windows`的启动项。

#### 本地化-中文

中文字体

```
sudo pacman -S adobe-source-han-sans-cn-fonts
```

去设置-> `Regional Settings`->`Region & Language`->`Change Language`->`简体中文`->`Apply`->`Restart now`->`OK`

#### 配置国内源下载必要应用

```
sudo nano /etc/pacman.conf
```

开启32位库

改

```
#[mulitlib]
#Include = /etc/pacman.d/mirrorlist
```

为

```
[mulitlib]
Include = /etc/pacman.d/mirrorlist

[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

保存退出后刷新

```
sudo pacman -Syy
```

导入cn源密钥

```
sudo pacman -S archlinuxcn-keyring
```

安装后端程序使得应用商店刷新出软件

```
sudo pacman -S archlinux-appstream-data packagekit-qt5 fwupd
```

安装`AUR`助手`yay`

```
sudo pacman -S yay
```

刷新

```
yay -Syy
```

安装中文输入法`Fcitx5`(Wiki上有)

```
sudo pacman -S fcitx5-im
```

```
sudo pacman -S fcitx5-chinese-addons fcitx5-rime
```

编辑`environment`

```
sudo nano /etc/environment
```

添加

```
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
INPUT_METHOD=fcitx
GLFW_IM_MODULE=ibus
```

不想添加`environment`也可以

```
yay -S fcitx5-input-support
```

重启后就可以在设置里找到拼音输入法了，至此，安装完成。

#### ERRO:主板更换后找回grub

**问题描述**

打开电脑后自动进入`Windows`系统，痛失`grub`引导

进入`UEFI/BIOS`里，启动方式那里只有`windows mangemer`了，原本的`grub`选项丢失了。

> windows mangemer在windows磁盘下的efi里，grub在linux磁盘下的efi里。

**解决方案**

进入安装时的live环境，连接wifi(即前文中的[`启动盘制作及分区`,`WIFI连接部分`])。

挂载分区(一定要先挂载根分区)

挂载根分区（位置在`/dev/nvme1n1p3`)

```
mount /dev/nvme1n1p3 /mnt
```

挂载home分区（位置在`/dev/nvme1n1p4`）

```
mount --mkdir /dev/nvme1n1p4 /mnt/home
```

挂载efi分区(位置在`/dev/nvme1n1p2`)

```
mount --mkdir /dev/nvme1n1p2 /mnt/efi
```

挂载交换分区（位置在`/dev/nvme1n1p5`）

```
swapon /dev/nvme1n1p5
```

`arch-chroot`进入系统

```
arch-chroot /mnt
```

重新安装`grub`服务

```
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=grub
```

更新`grub`引导

```
grub-mkconfig -o /boot/grub/grub.cfg
```

退出系统，重启电脑即可进入`Archlinux`桌面环境工作

```
exit
```

```
reboot
```

重启后在`grub`引导中并未看到`Windows`选项，正常现象

我们进入`Arch`,重新更新下，下次就有了

```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

从回显信息我们可以看到，`grub`找到了`windows`的启动项。

再次重启，验证结果，可以看到`grub`已经被我们成功修复。

```
reboot
```

### 软件生态配置

#### 纯软件

`Archlinux`中下载软件推荐上[Archwiki](https://wiki.archlinux.org/)（输入软件名即可，内有中文社区，点`AUR`中也可）

```bash
sudo pacman -S 软件包名（官方版）
```

```bash
yay -S 软件包名（AUR包）
```

自己下载解压安装亦可，初始环境配置推荐下面这篇文章

https://zhuanlan.zhihu.com/p/617640635

补充：

`Clash for Windows`可以用[Clash Verge](https://github.com/zzzgydi/clash-verge/releases/tag/v1.3.8)代替

> [翻过高墙（passwd:horton）](https://www.hortonyyx.com/article/over-the-wall)

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212659&authkey=%21ANiKrfTOh3bHFkY&width=1918&height=1036" width="1918" height="" />

系统代理设置这里和`Clash for windows`一致

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212661&authkey=%21ANiKrfTOh3bHFkY&width=1290&height=912" width="1290" height="" />

#### `Zsh`配置

参考文章（视频）：

[zsh安装](https://www.youtube.com/watch?v=8Uh9-BpTqfM) 来源于`Youtube`的一则视频

[zsh安装](https://wiki.archlinux.org/title/Zsh) 来源于`Archwiki`的一篇文章（主）

[oh-my-zsh安装](https://zhuanlan.zhihu.com/p/35283688) 来源于`知乎`的一篇文章

[oh-my-zsh安装](https://www.youtube.com/watch?v=f5bdi7WrIFs) 来源于`Youtube`的一则视频（主）

[p10k字体配置](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k) 来源于`Github`该主题作者的推荐字体（这里的字体很有用，解决了`Arch`图标显示不正常的问题）

官方字体下载，链接里有这四个字体，点击链接下载下来。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212660&authkey=%21ANiKrfTOh3bHFkY&width=1750&height=846" width="1750" height="" />

下载以后鼠标右键安装到系统用户即可（右键选中`ttf`文件有安装选项）

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212658&authkey=%21ANiKrfTOh3bHFkY&width=1232&height=764" width="1232" height="" />

字体下载（主要是视频里做的，但是效果不如作者推荐的字体）

```zsh
sudo pacman -Syu noto-fonts noto-fonts-emoji noto-fonts-extra awesome-terminal-fonts
```

配置

```zsh
sudo pacman -Syu zsh
```

```zsh
sudo pacman -Syu git
```

一切之前：

```bash
su root
cd 
```

`zsh`下载

```bash
pacman -S zsh
```

```bash
pacman -S zsh-completions
```

更改`shell`为`zsh`

```bash
chsh -l				#查看所有Shell所在位置
```

```bash
chsh -S 想更换的shell的完整目录
# 这里在哪个用户下更改的就是更换哪个用户的Shell
# 在root下更改后用户的shell仍然是原shell
# 重启电脑后shell生效
```

下载`oh-my-zsh`，配置文件在`/root/.zshrc`

```bash
sh -c "$(curl --insecure -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
```

##### 插件下载

[语法高亮](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md)

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

[自动补全](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md)

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

[主题下载](https://github.com/romkatv/powerlevel10k#oh-my-zsh)

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

##### 文件配置

```zsh
vim .zshrc
```

主题配置

```bash
# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes

ZSH_THEME="powerlevel10k/powerlevel10k"
#ZSH_THEME="robbyrussell"
```

插件配置

```bash
# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
        git
        zsh-autosuggestions
        # zsh-syntax-highlighting must be the last
        zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh
```

让配置生效，同时根据选择主题详细配置即可

```bash
source .zshrc
```

重新配置

```zsh
p10k configure
```

安装`i3`桌面

> 按照个人喜好，我这里pwngdb分屏显示特别奇怪，故仅仅进行了安装，并未实际使用

[i3 安装配置](https://doc.houdunren.com/%E6%95%88%E7%8E%87%E6%8F%90%E5%8D%87/3%20i3.html#%E5%9F%BA%E7%A1%80%E6%8E%A7%E5%88%B6)

> `blueman`暂时没什么用,他给的配置文件不咋好用，不建议使用

`vim/nvim`配置(更推荐`vim`)

[Archwiki_Neovim](https://wiki.archlinuxcn.org/wiki/Neovim)

[Archwiki_Vim](https://wiki.archlinuxcn.org/wiki/Vim)

#### `wine`下载配置

>不推荐，很多软件都不稳定，有时候能打开，有时候不能。小问题一堆，强烈不推荐

安装必要的包：

```bash
sudo pacman -S wine wine-mono winetricks zenity
```

保存并退出，重启系统

进入桌面后运行：

```bash
winecfg
```

把操作系统改为`Windows 10`

安装完后输入`vim ~/.bashrc`，往里面插入：

```bash
export WINEARCH="win32"
```

百度网盘里下载Windows下的字体文件到`~/.wine/drive_c/windows/Fonts`，然后运行：

```bash
winetricks
```

选择`Select the default wineprefix`（选择默认的wine容器），然后再选择`Run uninstaller`，单击`Install...`选择安装包安装程序

> 一定要安装字体，不然打不开ida

最后在终端输入`wine <exe>`运行你喜欢的程序吧！

安装`python_for_windows`:下载后打开目录，运行

```bash
wineconsole
```

在终端执行安装命令安装`python`环境（必须装，不然无法载入反编译二进制文件）

```bash
./python-3.12.0-amd64.exe
```

```bash
chmod +777 python安装位置在linux环境的映射
```

参考文档:

 [[详解]ArchLinux下Wine的使用](https://blog.csdn.net/qq_45933858/article/details/124553135)

[使用 Wine 运行 IDA Pro](https://www.csdzds.cn/posts/linux-wine/)

### WPS

粗体字体问题

```zsh
yay -S freetype2-wps 
```

启动时跳出的缺失字体问题

```zsh
yay -S ttf-wps-fonts
```

