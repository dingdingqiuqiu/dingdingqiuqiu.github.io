---
title: CS61b食用指南(WEEK1)
date: 2024-03-14 22:21:08
tags:
categories:
---

针对第一周的内容做了具体总结归纳。
<!--more-->

## 课程



## 实验01-设置

### 安装git

>  git的自动登录和简单使用在原先的文章中已经有所涉及:
>
> [点此跳转](https://dingdingqiuqiu.github.io/tags/Git/)

但是，CS61b专门提供了一篇`git`深度指南。必要时可以做一次汉化实践，先当指南看着。

[CS61b官方git指南](https://sp24.datastructur.es/resources/guides/git/)

这里先就此复习下**远程存储库**部分(实际上是CS61b当时的要求)

- `git clone [remote-repo-URL]`：在本地计算机上创建指定存储库的副本。还创建一个工作目录，其中的文件排列方式与下载存储库中的最新快照完全相同。还记录用于后续网络数据传输的远程存储库的 URL，并为其赋予特殊的远程存储库名称“origin”。
- `git remote add [remote-repo-name] [remote-repo-URL]`：记录网络数据传输的新位置。
- `git remote -v`：列出网络数据传输的所有位置。
- `git pull [remote-repo-name] main`：获取文件的最新副本，如 中所示`remote-repo-name`。
- `git push [remote-repo-name] main`：将文件的最新副本上传到`remote-repo-name`。

对于本课程的大部分时间，您只有两个远程存储库：

`origin`，它是用于保存和提交您的个人工作的远程存储库

`sculpture`，它是您将从中接收框架代码的远程存储库。

这里`git`初始配置贴一个没见过（使用过）的：

```zsh
git config --global pull.rebase false
```

> `pull.rebase` 是一个配置项，它决定了 `git pull` 命令的行为。如果设置为 `true`，那么在执行 `git pull` 命令时，Git 会使用 `rebase` 而不是 `merge` 来合并远程仓库的新提交。`rebase` 会把你本地的提交放到远程仓库的新提交之后，这样可以得到一个线性的提交历史，使得历史更加清晰。

### 这块不懂，建议写篇博客专门实践下。

**举例说明**

假设你正在开发一个项目，并且你有一个名为 `feature` 的分支，你正在这个分支上工作。同时，你的同事在 `master` 分支上也做了一些改动，并且他们已经将这些改动推送到了远程仓库。

现在，你想要获取 `master` 分支上的最新改动。你有两种方式可以做到这一点：`merge` 和 `rebase`。如果你执行 `git pull` 命令，Git 默认会使用 `merge` 来合并远程仓库的新提交。

这就是 `git config --global pull.rebase false` 这个命令的作用。它会设置 Git 的 `pull` 命令在合并远程仓库的新提交时，使用 `merge` 而不是 `rebase`。

例如，你的提交历史可能会像下面这样：

```
A - B - C (master)
     \
      D - E (feature)
```



当你在 `feature` 分支上执行 `git pull origin master` 后，如果你的 `pull.rebase` 配置项被设置为 `false`，那么你的提交历史会变成：

```
A - B - C - F (master)
     \       /
      D - E (feature)
```



这里的 `F` 就是一个新的 “合并提交”，它包含了 `master` 分支和 `feature` 分支的所有改动。

如果你的 `pull.rebase` 配置项被设置为 `true`，那么在执行 `git pull origin master` 后，你的提交历史会变成：

```
A - B - C (master)
         \
          D' - E' (feature)
```



这里的 `D'` 和 `E'` 是 `D` 和 `E` 的重新应用。它们的改动和 `D` 和 `E` 是一样的，但它们是全新的提交，因为它们的父提交是 `C`，而不是 `B`。

所以，`git config --global pull.rebase false` 这个命令的意思就是在全局范围内，设置 `git pull` 命令在合并远程仓库的新提交时，使用 `merge` 而不是 `rebase`。这是 Git 的默认行为，如果你没有特别的需求，一般不需要执行这个命令。

### Git 存储库和 Java 库

### Java 库

就像在 Python 中一样，我们有时想使用其他人编写的库。Java 依赖项管理有点混乱，因此我们提供了一个 git 存储库，其中包含我们将在本课程中使用的所有依赖项。再次确保您的终端已打开。

导航到您要存储库的文件夹。对于本实验，我们假设您将所有内容放置在名为**cs61b**的文件夹中。如果您愿意，可以选择不同的名称。`cs61b`导航到您想要的位置、创建目录并进入该目录后（`cd cs61b`在本例中），它可能看起来像这样：

![终端目录](https://sp24.datastructur.es/labs/lab01/img/terminal_directory.png)

进入文件夹后，运行：

```
git clone https://github.com/Berkeley-CS61B/library-sp24
```

下面是 的目录结构`library-sp24`。使用查看文件夹内部 `ls library-sp24`并确保您看到`.jar`下面列出的文件。还有很多，但我们只列出前几个。如果您使用操作系统的文件资源管理器，该`jar`部分可能不会显示在文件名中，但这没关系。

```
library-sp24
├── algs4.jar
├── animated-gif-lib-1.4.jar
├── antlr4-runtime-4.11.1.jar
├── apiguardian-api-1.1.2.jar
└── ...
```

### 个人储存库

```
CS61b/
```

```zsh
git clone git@github.com:dingdingqiuqiu/CS61B-sp24.git
```

```zsh
cd CS61B-sp24
```

```zsh
git branch -M main  
```

```zsh
git remote add skeleton https://github.com/Berkeley-CS61B/skeleton-sp24.git 
```

```zsh
git pull skeleton main
```

课程实验库拉取成功

### IntelliJ 设置

查看[IntelliJ WTFS 指南](https://sp24.datastructur.es/resources/guides/intellij/wtfs/)以获取一些常见问题的解决方案。

安装CS61b插件

![搜索 CS 61B](https://sp24.datastructur.es/labs/lab01/img/plugin_setup2.png)

现在，搜索“Java Visualizer”，然后单击绿色的**安装**按钮来安装该插件。

![搜索 Java 可视化工具](https://sp24.datastructur.es/labs/lab01/img/plugin_setup3.png)

### 安装Java



## 作业

