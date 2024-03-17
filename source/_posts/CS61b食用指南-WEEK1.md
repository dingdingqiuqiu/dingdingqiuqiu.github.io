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
# git clone git@github.com:dingdingqiuqiu/CS61B-sp21.git  
```

```zsh
cd CS61B-sp24
# cd CS61B-sp21  
```

```zsh
git branch -M main  
```

```zsh
git remote add skeleton https://github.com/Berkeley-CS61B/skeleton-sp24.git 
# git remote add skeleton https://github.com/Berkeley-CS61B/skeleton-sp21.git
```

```zsh
git pull skeleton main
# git pull skeleton master
```

课程实验库拉取成功

> 后续发现sp21有开源的自动评分机器，又开了个sp21的仓库。
>
> 注释部分为sp21版
>
> 将普通用户赋权root组，又配置了下ssh普通用户的rsa密钥。
>
> gpasswd -a P4yl04d root   
>
> 赋权命令

### IntelliJ 设置

查看[IntelliJ WTFS 指南](https://sp24.datastructur.es/resources/guides/intellij/wtfs/)以获取一些常见问题的解决方案。

安装CS61b插件

![搜索 CS 61B](https://sp24.datastructur.es/labs/lab01/img/plugin_setup2.png)

现在，搜索“Java Visualizer”，然后单击绿色的**安装**按钮来安装该插件。

![搜索 Java 可视化工具](https://sp24.datastructur.es/labs/lab01/img/plugin_setup3.png)

### 安装Java

> 略

### 习题

打开名为`Collatz.java`. 尝试运行它，您将看到打印出数字 5。

该程序应该打印从给定数字开始的[Collatz 序列。](https://en.wikipedia.org/wiki/Collatz_conjecture)Collatz 序列定义如下：

如果 n 是偶数，则下一个数字是 n/2。如果 n 是奇数，则下一个数字是 3n + 1。如果 n 是 1，则序列结束。

例如，假设我们从 5 开始。由于 5 是奇数，所以下一个数字是 3x5 + 1 = 16。由于 16 是偶数，所以下一个数字是 8。由于 8 是偶数，所以下一个数字是 4。由于 4 是偶数下一个数字是 2。由于 2 是偶数，所以下一个数字是 1。此时我们就完成了。顺序是 5、16、8、4、2、1。

您的第一个任务是编写一个方法，如下所示：`public static int nextNumber(int n)`返回下一个数字。例如`nextNumber(5)`应返回 16。此方法将由 Gradescope 自动评分器进行测试。确保提供该方法的描述作为注释。您的描述应包含在`/**`和中`*/`。包含的注释`/**`也`*/`称为“Javadoc 注释”或简称为“Javadoc”。如果这些注释需要额外的空间，则可以跨越多行，例如 Javadocs `nextNumber`。

Javadoc 可能包含可选标签，例如`@param`. 除了 标签外，我们不要求您在 61B 中使用任何类似的标签`@source`。`@source`每当您在项目上获得重要帮助时，请使用该标签。`@source`硬件或实验室不需要该标签，但我们还是推荐它，因为引用来源是一个良好的学术和专业习惯。

一些 Java 技巧：

- 运算`%`符实现余数。例如， 的值`x % 4`将为 `0`、`1`、`2`或`3`。
- 该`==`运算符比较两个值是否不相等。代码片段`if (n % 4 == 1)`读作“如果 n 除以 4 的余数等于 1”。

编写完成后`nextNumber`，填写`main`方法，使其打印出从 开始的 Collatz 序列`n = 5`。例如，如果`n = 5`，您的程序应该打印`5 16 8 4 2 1`。如果1后面有一个额外的空格就可以了。

有趣的事实：对于所有数字，Collatz 序列似乎都终止于 1。然而，到目前为止，没有人能够证明这对于所有可能的起始值都是正确的，但已检查了大约 2^68 以内的所有值。正如维基百科文章中所述，数学家杰弗里·拉加里亚斯指出，科拉茨猜想“是一个极其困难的问题，完全超出了当今数学的范围”。

## 作业

