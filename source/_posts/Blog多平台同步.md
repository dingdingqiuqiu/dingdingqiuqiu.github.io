---
title: Blog多平台同步
date: 2023-11-20 22:22:17
tags:
---

### 当前配置

> 博客搭建推荐视频：[手把手教你搭建属于自己的hexo+github博客](https://www.bilibili.com/video/BV1cW411A7Jx/?spm_id_from=333.999.0.0&vd_source=13e2561436a72606df7175a92524fa50)

> 图床推荐使用Onedrive : [图床折腾之旅](https://www.hortonyyx.com/article/image-bed)

> 博客迁移原理视频：[备份和迁移hexo博客](https://www.bilibili.com/video/BV13g41147yV/?spm_id_from=333.999.0.0&vd_source=13e2561436a72606df7175a92524fa50)

> `Windows`:`hexo`部署平台所在地，已生成一定量的博客托管在`Github`上

> `Arch`:新系统,已有`nodejs`环境。

> `Github`：博客托管平台，`main`为主分支，计划以`master`分支作为部署文件。

更改`master`分支为默认分支（方便`Arch`通过`git clone`获得部署文件)

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212640&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212639&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

选择后点`Update`即可，最后结果是

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212642&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

把该分支克隆到本地，进入`dingdingqiuqiu.github.io`文件夹，可以看到分支为`master`

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212644&authkey=%21ANiKrfTOh3bHFkY&width=1728&height=944" width="1728" height="" />

删除原分支除`.git`外的所有文件（这里误删了`.git`，后面将其恢复了），并将原`dingdingqiuqiu`文件里除`.deploy_git`外所有文件移动到`dingdingqiuqiu.github.io`

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212646&authkey=%21ANiKrfTOh3bHFkY&width=1558&height=740" width="1558" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212648&authkey=%21ANiKrfTOh3bHFkY&width=1914&height=952" width="1914" height="" />

安装一些必要插件（可能不是很必要，装上也没啥影响）

```cmd
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
npm install hexo-generator-search --save
npm install hexo-generator-searchdb --sava
```

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212650&authkey=%21ANiKrfTOh3bHFkY&width=1481&height=944" width="1481" height="" />

最后剩下的文件结构

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212652&authkey=%21ANiKrfTOh3bHFkY&width=1710&height=952" width="1710" height="" />

注意到这里存在`.gitignore`文件，如果不存在，创建并写入

```bash
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
_multiconfig.yml
```

>  注意，如果之前克隆过`theme`里的主题文件，应该把主题文件中的`.git`删除，因为`git`无法嵌套上传（我这里看了下，仅有`.github`，无`.git`，因此不必删除`.git`）

最后将所有更改提交即可

```cmd
git add .
```

```cmd
git commit -m "new hexo file"
```

```cmd
git push
```

提交后看下两个分支，`main`为静态`HTML`,`master`为博客部署文件，符合预期。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212656&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212655&authkey=%21ANiKrfTOh3bHFkY&width=1920&height=924" width="1920" height="" />

Linux下

```zsh
git clone git@github.com:dingdingqiuqiu/dingdingqiuqiu.github.io.git
```

```zsh
#赋予文件夹被修改的权限，不然无法编辑
chmod -R 777 dingdingqiuqiu.github.io.git
```

```zsh
cd dingdingqiuqiu.github.io
```

```zsh
pacman -S npm
```

```zsh
##对post文件进行修改
........
```

```zsh
git add .
```

```zsh
git commit -m "Arch's commit"
```

```zsh
git push
```

最后，两个平台在写博客之前先同步一下

```zsh
git pull
```

参考文档：

[Hexo博客多台电脑设备同步管理](https://juejin.cn/post/6844903590474022925)

[Hexo多台电脑同步](https://www.cnblogs.com/shuofxz/p/11736825.html#0-%E8%A7%A3%E5%86%B3%E6%80%9D%E8%B7%AF)

