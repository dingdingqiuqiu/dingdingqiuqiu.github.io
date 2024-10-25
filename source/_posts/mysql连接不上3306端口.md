---
title: mysql连接不上3306端口
date: 2024-09-23 01:58:07
tags:
categorie:
---

经过多次尝试，发现是安装时配置出现了问题，密码选项哪里不要选推荐的配置，选传统密码选项即可。不然解决连不上3306端口的问题以后还会被禁止登录。

<!--more-->

可以参考这篇的[配置](https://blog.csdn.net/weixin_47406082/article/details/131867849)

**注意**

1.自定义安装可以指定sql安装位置。

2.密码策略选择传统，否则极有可能出现下面两种报错。

> 启动MySQL报错:ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (10061)

> ERROR 1045 (28000): Access denied for user ‘root‘@‘localhost‘ (using password: NO/YES)
