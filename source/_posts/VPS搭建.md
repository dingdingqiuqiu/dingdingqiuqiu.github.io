---
title: VPS搭建
date: 2023-11-19 16:35:50
tags: 
- VPS
categories: 
- 环境搭建
- 开发环境
---
本文是`VPS`搭建教程
<!--more-->
# 最近更新

**最初放弃机场尝试自建节点是由于玩双人成行时EA平台上好友列表加载不出来，怀疑是机场节点问题，后面搭建到一半发现用全局直连开TUN模式可以解决这个问题，遂放弃，有必要的话再考虑自建吧，先写到这**

> 一定要用**DIRECT**模式，其他节点好友列表均加载不出来，EA在线状态也显示不在线，具体原因未知

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212622&authkey=%21ANiKrfTOh3bHFkY&width=1070&height=758" width="1070" height="" />

### 参考文档

[【科学上网一键搭建系列一】新手如何免费开启VPS一站式入门教学，零基础必看视频，助你一学就会，快速上手，以后基操不再求人。](https://www.youtube.com/watch?v=g21MJc7SG_c&t=400s)

> `Youtube`上的一则视频，侧重VPS的选择

[最新零基础保姆级小白节点搭建教学-不良林](https://www.youtube.com/watch?v=s90feRmdr9A&t=6s)

> `Youtube`上的一则视频，侧重具体搭建过程

### VPS选择

1.**VULTR**

> 支持小时收费			更换IP免费			推荐美西机房

注册（这里我用的`github`邮箱，密码要大于十个字符（不必与`Github`密码一致**@github**））

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212627&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=980" width="1920" height="" />

右上角`Deploy`选择`Deploy New Server`,进入以后,`Server`和`CPU`选最基础的即可

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212625&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212623&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

位置这里选择，纽约的比洛杉矶便宜一些，系统选择`CentOS7`即可，注意把自动备份关掉，省点钱可以。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212626&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212624&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

### 搭建教学

#### `Finalshell`下载

[`Finalshell`多平台下载地址](http://www.hostbuf.com/downloads/finalshell_install.exe)
