---
title: nodejs版本控制
mathjax: true
date: 2025-02-27 13:30:42
tags:
categories:
---

本文主要记录nodejs版本控制工具nvm的安装配置

<!--more-->

原文链接：https://juejin.cn/post/6844904014899838983
nvm下载：https://github.com/coreybutler/nvm-windows/releases
需要注意的是安装路径最好是C:\nvm，默认的路径安装成功后，在切换node版本时会有问题。
下载完找到nvm的安装目录，打开settings.txt文件，添加上下面两个配置：
```
nvm node_mirror https://npm.taobao.org/mirrors/node/
nvm npm_mirror https://npm.taobao.org/mirrors/npm/
```

常用的一些nvm命令

nvm install  [arch]：该可以是node.js版本或最新稳定版本latest。（可选[arch]）指定安装32位或64位版本（默认为系统arch）。设置[arch]为all以安装32和64位版本。

nvm list [available]：列出已经安装的node.js版本。可选的available，显示可下载版本的部分列表。这个命令可以简写为nvm ls [available]。

nvm uninstall ： 卸载指定版本的nodejs。

nvm use [version] [arch]： 切换到使用指定的nodejs版本。可以指定32/64位[arch]。

还有一些其他的命令就不一一距举例了
