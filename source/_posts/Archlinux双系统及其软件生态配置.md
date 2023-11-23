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

