---
title: linux环境中matplotlib画图缺少字体
date: 2024-09-15 00:25:58
tags: 机器学习
categories:
---
本文主要解决linux环境下画图没中文字体的问题
<!--more-->

### 问题描述

倘若使用的linux没有x协议支持，无法使用`plt.show`函数绘图，转而使用`plt.savefig`和`plt.close`函数保存图片查看。我们使用以下代码添加中文支持。

> 补充: `feh`可以查看png图片，`apt-get install fet`安装它，`feh example.png`查看图片。

```python
plt.rcParams['font.sans-serif']=['SimHei']
plt.rcParams['axes.unicode_minus'] = False
```
但会出现下面报错<img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VTNGtwRFlHU3RRZ2dQdUtEUUFBQUFBQjJWNm54V242VUx4Q1BQQW80RjFBelE_ZT0zd3A2blc.png" width="958" height=" " />

### 解决方法

### 字体下载安装

- 下载：
  [SimHei字体下载链接](https://us-logger1.oss-cn-beijing.aliyuncs.com/SimHei.ttf)

- 安装：

将字体放到对应文件夹

```zsh
sudo cp SimHei.ttf /usr/share/fonts/SimHei.ttf
```

赋予使用权限

```zsh
sudo chmod 777 /usr/share/fonts
```

### 配置matplotlib

#### 方法1.删除缓存

> 已尝试，有效

查看`matplotlib`的缓存

```python
import matplotlib as mlt

print(mlt.get_cacahedir())
# /home/ubuntu22/.cache/matplotlib 
```

删除缓存

```
rm -rf /home/ubuntu22/.cache/matplotlib 
```

#### 方法2.在matplotlib对应字体文件夹下更改

查看`matplotlib`配置文件位置

```python
import matplotlib

print(matplotlib.matplotlib_fname())
# /home/ubuntu22/henu/ML/ml/lib/python3.10/site-packages/matplotlib/mpl-data/matplotlibrc
```

由输出，我们得到字体目录为

```
/home/ubuntu22/henu/ML/ml/lib/python3.10/site-packages/matplotlib/mpl-data/fonts/ttf
```

我们把安装好的字体复制过去一份

```
cp /usr/share/fonts/SimHei.ttf /home/ubuntu22/henu/ML/ml/lib/python3.10/site-packages/matplotlib/mpl-data/fonts/ttf/SimHei.ttf 
```



