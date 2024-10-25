---
title: wsl配置使用notebook
date: 2024-09-15 02:40:40
tags: 机器学习
categories:
---

一直使用linux终端比较习惯，这里展示wsl优雅启动notebook,以及基本使用

<!--more-->

### jupyter配置

#### 生成默认配置文件

```
jupyter notebook --generate-config
```

会在用户目录下生成`.jupyter`文件夹，其中`jupyter_notebook_config.py`就是配置文件。

#### 生成密钥

终端输入`ipython`进入命令行

```zsh
ipython
```

输入密码

```
from jupyter_server.auth import passwd
passwd()
```

or直接在shell

```
jupyter server password
```

如果采用方式1，直接在命令行打印了密钥，如果采用了方式二，会提示密钥写在了何处，我们查看并复制，下一步配置自动登录时会用到

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7mwzYiq30zod2xxZG?embed=1&width=940&height=253" width="940" height=" " />

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7mw7Yiq30zod2xxZG?embed=1&width=958&height=95" width="958" height=" " />

#### 配置文件修改

默认自动登录，打开第一步生成的配置文件，把hash写入，仅手动登录一次后即可实现自动登录

```
vim ~/.jupyter/jupyter_notebook_config.py 
```

修改下面`c.ServerApp.password `的值为上面的密钥即可。

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7mw_Yiq30zod2xxZG?embed=1&width=958&height=1018" width="958" height=" " />

编辑默认生成的配置文件

```
vim ~/.jupyter/jupyter_notebook_config.py
```

对下面这行解注释即可

```
c.NotebookApp.open_browser = False     	#不自动打开浏览器
```

### jupyter使用

#### nohup方式

我们用下面这行命令打开`notebook`没有额外输出，终端可以继续干别的事情

```
nohup jupyter notebook&
```

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7mxDYiq30zod2xxZG?embed=1&width=958&height=202" width="958" height=" " />

该命令返回的一个pid,我们可以使用`kill`命令来杀死它，注意这里我们实际上是将输出写入了当前目录下的`nohup.out`,如果太大，记得删除。我们想要停止该进程可以使用`kill -9 pid`停止。

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7mxHYiq30zod2xxZG?embed=1&width=958&height=390" width="958" height=" " />

另一种方式是使用`tumx`启动`notebook`

#### `tmux`方式

> 会介绍的这些已经可以很优雅得使用nootbook了
>
> 实际上tmux相当好用，还有别的有趣功能

安装：
```
sudo apt-get install tmux
```

进入：

```
tmux
```

之后在终端上下左右切分切换即可。如下图所示。上方窗口在下方`notebook`启动后依旧可以执行其他命令。

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7mxLYiq30zod2xxZG?embed=1&width=1919&height=1018" width="1919" height=" " />

由于`tmux`快捷键设置较为阴间，我们配合neovim的窗口快捷键切换出一篇blog介绍统一方便的快捷键设置。可跳转至下面这篇文章:

[Neovim与Tmux快捷键设置[myblog]](https://dingdingqiuqiu.github.io/2024/09/15/Neovim%E4%B8%8ETmux%E5%BF%AB%E6%8D%B7%E9%94%AE%E8%AE%BE%E7%BD%AE/#more)

