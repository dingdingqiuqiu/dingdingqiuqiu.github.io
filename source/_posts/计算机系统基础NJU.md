---
title: 计算机系统基础NJU
date: 2023-11-17 22:22:17
tags: 
- C语言字面量的数据类型
- gcc编译32位程序
categories:
- OS
- date
---
本文是对《计算机系统基础》一书中第一章C语言中字面量数据类型认定实验的复现,主要涉及C语言字面量数据类型，gcc编译32位程序两个知识点。重点在于计算机在进行比较时不会先看正负号认定数据类型，而是根据数据绝对值的大小认定。
<!--more-->
## c语言数值常量的类型

安装`gcc`多平台编译工具

```zsh
pacman -S gcc-multilib    
```

安装32位程序所需的`libc`库

```zsh
pacman -S lib32-glibc lib32-gcc-libs
```

实验代码

```c
#include <stdio.h>

int main(){
        if(-2147483648>2147483647)
                printf("false!!!");
        else
                printf("true!!!");
        return 0;
}
```

实验结果

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212635&authkey=%21ANiKrfTOh3bHFkY&width=1914&height=374" width="1914" height="" />

关键点在于理解计算机先不管符号，根据`2127483648`确定类型为`unsigned int`，再加上符号，对`0x80000000`进行补码转换又得到`0x80000000`，由于先前已经确定`unsigned int`类型，所以比较时，以`2147483648(0x80000000)`和`2147483647(0x7fffffff)`比较，满足大于条件，输出`false`。

参考下文：

[计算机系统基础读书笔记摘要](https://blog.csdn.net/gzxb1995/article/details/104334278)

