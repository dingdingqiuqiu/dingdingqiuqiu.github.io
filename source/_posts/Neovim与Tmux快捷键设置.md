---
title: Neovim与Tmux快捷键设置
date: 2024-09-15 04:19:18
tags: vim, tmux
categories: 
---

本文主要对个人的vim以及tmux的快捷键进行了简单统一的映射，仅个人所用。

<!--more-->

由于`ctrl`键经常需要用而且位置阴间，推荐下面这个工具进行`ctrl`键和`capsLk`键的键位修改。小拇指蒸滴受不了了要....

[如何操作[知乎回答]](https://zhuanlan.zhihu.com/p/412076746)
[下载地址[github]](https://github.com/microsoft/PowerToys/releases/tag/v0.84.1)

### vim 快捷键

#### V模式

> 不选中默认一行，选中可以多行
>
> ”V“：visual line和"v":visual模式均适用

快速移动单行（成块）："J"和"K"

#### 增加窗口

水平增加窗口：主键加sv

> 我的主键是空格

```
<leader>sv
```

垂直增加窗口

```
<leader>sh
```

退出窗口

```
:q
```

#### 取消高亮

```
<leader>nh
```

#### 文档树

打开/关闭

```
<leader>e
```

展开文件夹,展示文件内容（光标依旧在文档树）

```
<tab>
```

打开文件（光标在文件内容中）

```
o
```

```
<enter>
```

创建新文件，在文档树中想添加新文件的位置输入

```
a
```

底部会出现文件名，输入完成后按`<enter>`即可

#### 跳转

查看函数/变量定义

```
gd
```

#### 自动补全

按`<tab>`可以选择想要自动补全的内容，按`<enter>`输入自动补全。对于自动补全的各个参数，按`<tab>`输入下一个参数，按`<shift>+<tab>`输入上一个参数。安装新的语言服务：

```
:Mason
```

搜索需要的服务，按`i`安装即可。

取消补全

```
<ctrl>e
```

函数文档查看

```
<shift>K
```

补全文档翻阅

```
<ctrl>b
```

```
<ctrl>f
```

#### 注释

单行注释/解注释

```
gcc
```

多行注释/解注释

```
gc
```

#### 缓冲区

缓冲区间切换

向右切换缓冲区

```
<shift>L
```

向左切换缓冲区

```
<shift>H
```

#### 文件检索

> telescope插件

文件名查找(Find Filename)

```
<leader>ff
```

文件内容查找(Find grep)

```
<leader>fg
```

缓冲列表查找(Find Buffer)

```
<leader>fb
```

帮助文档(Find Help)

```
<leader>fh
```

#### vim-tmux

借助`vim-tmux-navigator`插件实现窗格切换

> 详细配置过程可见以下blog：
> [vim-tmux-navigator配置使用](https://dingdingqiuqiu.github.io/2024/09/16/vim-tmux-navigator%E9%85%8D%E7%BD%AE%E4%BD%BF%E7%94%A8/#more)

vim中窗口切换

```
<ctrl> h/j/k/l
```

tmux中窗口切换





