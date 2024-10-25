---
title: Arch配置Git自动登录验证
date: 2023-11-10 22:23:17
tags: 
- Git
categories:
- 环境搭建
- 开发环境
---
本文主要实现了`Archlinux`环境下`git push`指令免密登录的配置
介绍了`git`相关操作
<!--more-->
### Arch配置Git自动登录验证

参考文档：

[核心参考文档](https://zhuanlan.zhihu.com/p/571015337) 本文思路基本和该文一致，相当于这篇文章的详细版

[图解如何在Linux上配置git自动登录验证 ](https://www.cnblogs.com/apocelipes/p/14491762.html) (了解)

> 实际使用下来不如SSH免密登录，因为http免密登录需要储存凭证程序，而使用储存凭证程序需要与‘’Secret Service"建立联系，国内网络连接被拒......更推荐下面这片文章中的SSH免密登录

[GitHub不再支持密码验证解决方案：SSH免密与Token登录配置](https://cloud.tencent.com/developer/article/1861466) (推荐)

[github配置SSH免密登录](https://blog.csdn.net/qq_38163309/article/details/105335097) (更针对目前的Github版本，在Github上导入密钥可以参考这篇)

[github关于身份验证的官方文档](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)

为什么要配置git自动登录验证？

> 官方已经停用了在终端中利用git通过账户名密码的方式登录github

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212664&authkey=%21ANiKrfTOh3bHFkY&width=1898&height=178" width="1898" height="" />

> 因此配置SSH免密登录和Token登录是必要的

### 配置方式

生成`ssh-key`

```zsh
ssh-keygen -t rsa -C 1501608414@qq.com  
```

`ENTER`后会回显密钥储存位置，进入该位置

```zsh
cd /root/.ssh/
```

```zsh
ls
```

可以看到有两个文件`id_rsa`（私钥）和`id_rsa.pub`（公钥）

把公钥导入`Github`中的`Settings`中的`SSH and GPG keys`即可。

### git修改远程仓库地址

方法有三种：

1. 修改命令

   ```zsh
   git remote set-url --push origin git@github.com:dingdingqiuqiu/Leetcode.git
   ```

2. 先删后加

   ```zsh
   git remote rm origin
   ```

   ```zsh
   git remote add origin git@github.com:dingdingqiuqiu/Leetcode.git
   ```

3. 直接修改config文件

     git文件夹，找到config，编辑，把就的项目地址替换成新的。

### Git基本使用

> 该部分介绍基本够用，更详细的介绍在[dingdingqiuqiu-git手册](https://dingdingqiuqiu.github.io/2024/10/08/git%E6%89%8B%E5%86%8C/#more)

#### 首次使用

配置`Git`的全局账户名和电子邮箱

```zsh
git config --global user.name "P4yl04d"
```

```zsh
git config --global user.email "1501608414@qq.com"
```

使用以下命令检验配置结果

```zsh
git config --global user.name
```

```zsh
git config --global user.email
```

修改默认编辑器为`nvim`

```shell
git config --global core.editor nvim
```

`merge`时修改冲突文件工具`mergetool`

```shell
git config --global merge.tool vimdiff
git config --global merge.conflictstyle diff3
git config --global mergetool.prompt false
 
#让git mergetool不再生成备份文件(*.orig)  
git config --global mergetool.keepBackup false
```

#### 修改远程库

参考文档：

[官方文档](https://docs.github.com/zh/get-started/getting-started-with-git/managing-remote-repositories) （推荐）

[linux下，git配置github](https://zhuanlan.zhihu.com/p/571015337) (讲的很细，也推荐)

拉取库

```zsh
git clone https://github.com/dingdingqiuqiu/OS_NJU_Jsut2try
```

修改库

```zsh
cd OS_NJU_Jsut2try
```

```zsh
mv /home/P4yl04d/Documents/OS_NJU_Jsut2try_my/* .
```

```zsh
git add .
```

```zsh
git commit -m "M32_C90_unsigned_BUG_try"
```

```zsh
git remote -v
# 回显：
# origin  https://github.com/dingdingqiuqiu/OS_NJU_Jsut2try (fetch)
# origin  https://github.com/dingdingqiuqiu/OS_NJU_Jsut2try (push)
```

```zsh
git push -u origin mian
```

> `git push -u origin main` 命令会将本地分支 `main` 推送到远程仓库 `origin` 中的 `main` 分支。如果您想将本地分支推送到远程仓库的其他分支，可以使用以下命令：
>
> ```zsh
> git push -u <remote> <local_branch>:<remote_branch>
> ```
>
> 这个命令会将本地分支 `<local_branch>` 推送到远程仓库 `<remote>` 中的 `<remote_branch>` 分支。例如，如果您想将本地分支 `dev` 推送到远程仓库 `origin` 中的 `develop` 分支，可以使用以下命令：
>
> ```zsh
> git push -u origin dev:develop
> ```

#### 本地文件上传库

参考文档：
[How to use git](https://www.freecodecamp.org/news/introduction-to-git-and-github/#how-to-start-using-git-and-github)

[官方文档](https://docs.github.com/zh/get-started/getting-started-with-git/managing-remote-repositories) (推荐)

首先在`Github`上创建新仓库（不添加READ.MD文件）,官方这里也有介绍，我解释下每一句的意思。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212663&authkey=%21ANiKrfTOh3bHFkY&width=1854&height=902" width="1854" height="" />

首先在本地创建文件夹用于上传文件到`Github`中的仓库  

```zsh
cd /home/P4yl04d/Documents                    
```

```zsh
mkdir Leetcode
```

进入文件夹，并对其初始化

```zsh
# 进入文件夹
cd Leetcode

# 创建README.md文件，并写入仓库名字
echo "# Leetcode" >> README.md

# 对文件夹进行初始化，主要是在该文件夹中创建.git文件，记录版本变化
# rm -rf .git可撤销初始化操作
git init

# 添加README.md文件到”预提交“处
git add README.md

# 进行实际提交命令，参数m用来标记提交信息
git commit -m "first commit"

# 把默认分支master**重命名**为main，让新版本被添加时处于main分支下
git branch -M main

# 添加一个名为origin的远程分支到git仓库
git remote add origin git@github.com:dingdingqiuqiu/Leetcode.git

# 把本地仓库的改变推送到远程仓库origin
# -u参数将当前分支的上游分支(upstream branch)设置成origin/main
#这允许您将来使用 git push 时无需指定远程名称和分支名称。
git push -u origin main
```

`checkout`代表切换到`master`分支，`-b`参数代表如果不存在`master`分支，那就创建一个`master`分支。

```zsh
git checkout -b master
```

#### Debug

在某次`git push`后

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212824&authkey=%21ANiKrfTOh3bHFkY&width=796&height=199" width="796" height=" " />

进入`.ssh`文件夹，新建`config`文件，输入以下内容即可

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212826&authkey=%21ANiKrfTOh3bHFkY&width=1213&height=762" width="1213" height="" />



```config
Host github.com
Hostname ssh.github.com
Port 443
User git
```

`Arch`也碰到了相同的问题，同样方法解决即可

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212827&authkey=%21ANiKrfTOh3bHFkY&width=1896&height=986" width="1896" height=" " />

参考文档:[ssh -T git@github.com ssh: connect to host github.com port 22: Connection refused 会报错_this key is not known by any other names.-CSDN博客](https://blog.csdn.net/m0_62159662/article/details/125156695?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1-125156695-blog-127533568.235^v39^pc_relevant_default_base&spm=1001.2101.3001.4242.2&utm_relevant_index=4)
