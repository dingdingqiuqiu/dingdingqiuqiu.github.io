---
title: CPP拾遗-输入输出
date: 2024-03-17 20:05:46
tags:
categories:
---

本文主要讲解C++中数据流相关概念以及std::getline()函数的使用

<!--more-->

### 问题描述

从控制台输入一对逗号分割的数据，将这对数据分别赋值给变量a和变量b。

代码实现如下

![image-20240317204249396](../../../../Pictures/Blog/CPP拾遗-输入输出/image-20240317204249396.png)

### 问题描述

利用字符串数字间转换的方法分割数字，求和操作。

![image-20240317222341961](../../../../Pictures/Blog/CPP拾遗-输入输出/image-20240317222341961.png)

应该注意的是`getline(cin,str)`使用时常常遇到一些bug,这是因为输入没有没清空，可以通过在`getline(cin,str)`前添加`getchar()`或者`cin.ignore()`来解决。
