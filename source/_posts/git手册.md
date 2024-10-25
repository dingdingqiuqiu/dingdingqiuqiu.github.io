---
title: git手册
date: 2024-10-08 14:20:10
tags:
categories:
---

结合他人blog或视频以及自己平时使用git的一些经验写成的一篇实用向git手册。

<!--more-->

参考视频：[十分钟速通git核心指令](https://www.bilibili.com/video/BV1Sp421o7U4)

> 博主分支部分有些错误，在本文已修正。

基础环境配置参见：[Arch配置Git自动登录验证](https://dingdingqiuqiu.github.io/2023/11/10/Arch%E9%85%8D%E7%BD%AEGit%E8%87%AA%E5%8A%A8%E7%99%BB%E5%BD%95%E9%AA%8C%E8%AF%81/#more)

### 拉取远程仓库并合并

> git fetch,git merge,git pull

`git fetch`命令拉取远程仓库最新代码，`git merge`执行合并。`git pull`拉取并自动合并。注意，`git merge`时可能会有`merge conflict`。为了避免合并时的冲突，可以先`git fetch`再`git merge`合并，这样根据IDE的报错修改特定冲突部分。

### 状态查询

> git log,git status

`git log`用来查看所有提交，`git status`用来查看本地文件夹的状态，`git diff`用来对比不同分支/提交的文件差异。

### 分支

> git branch

```shell
git checkout -b new_branch
```

通过以上命令在本地新建分支`new_branch`，该分支中文件在当前分支的基础上进行，同时自动进入`new_branch`分支。

```shell
git branch -vv
```

查看本地所有branch以及其与远程branch的绑定

进行一定开发后add,commit.........然后：

```shell
git push -u origin new_branch
```

将本地`new_branch`分支推送到远程`new_branch`，此时在远程新建了`new_branch`分支，并使用`-u`参数记住了该本地分支下次`git push/git pull`时需要推送/拉取的远程分支。

```shell
git switch main
```

本地切换到main分支。

```shell
git fetch origin new_branch
```

拉取远程的`new_branch`分支。

```shell
git merge origin/new_branch
```

合并本地`main`分支和远程`new_branch`分支

```shell
git push origin
```

将本地`main`分支的修改推送到远程仓库

### 解决冲突

> git的文件冲突解决方式比较智能，有时会因为错误的合并策略导致再次合并时不能重新合并，这是因为已经检测到合并过了，此时对要并入的文件略微添加注释即可重新合并。这是因为合并时会根据文件内容，合并历史，文件历史综合考虑合并策略。

合并时提示有冲突可以使用`mergetool`解决冲突。

```
git mergetool filename
```

> 若不加文件名，自动打开冲突文件

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7m1XYiq30zod2xxZG?embed=1&width=1274&height=817" width="1274" height=" " />

vimdiff使用

配置：

```shell
git config --global merge.tool vimdiff
git config --global merge.conflictstyle diff3
git config --global mergetool.prompt false
 
#让git mergetool不再生成备份文件(*.orig)  
git config --global mergetool.keepBackup false
```

键入以下命令启动

```
git mergetool
```

上面三个窗口分别是本地文件(LOCAL)，共同上游（BASE），远程文件(REMOTE)。下面的窗口为MERGED窗口(即合并后的窗口/有冲突的窗口)

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7m1bYiq30zod2xxZG?embed=1&width=1920&height=1032" width="1920" height=" " />

光标标移动到MERGED窗口，以下命令可以获取上面窗口的内容(也可根据个人需求自行修改MERGED窗口内容)

```SHELL
:diffget REMOTE # 获取REMOTE的修改到MERGED文件, 忽略大小写
:diffg REMOTE # 获取REMOTE的修改到MERGED文件, 忽略大小写
:diffg BASE # get from base
:diffg LOCAL # get from local
```

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7m1fYiq30zod2xxZG?embed=1&width=1920&height=1032" width="1920" height=" " />

上面这张图片键入命令无效，要求有一条白线(没有白线可以换成输入模式输入点内容自动白线)，如下：

<img src="https://1drv.ms/i/s!Ai4kpDYGStT7m1jYiq30zod2xxZG?embed=1&width=1920&height=1032" width="1920" height=" " />


还有一些其他命令

```shell
]c      # nect difference
[c      # previous difference
zo      # open folded text
zc      # close folded text
zr      # open all folds
zm      # close all folds
:diffupdate     # re-scan the file for difference
do      # diff obtain
dp      # diff put
```

解决冲突完成后`git comit`提交即可。

### fork

> 项目复制

在`github`上`fork`别人的远程仓库之后，相当于你自己新建了一个自己的远程仓库，只不过内容都是别人的远程仓库里的内容。你可以对自己`fork`到的仓库进行任意操作。例如，`git clone`到本地后，进行若干修改/提交，`git push`到的远程仓库为你的远程仓库。如果想让代码合并到原作者的远程仓库，需要向原作者申请`Pull requests`，原作者同意后即可合并到原作者的远程仓库。这即为开源项目的贡献流程。
