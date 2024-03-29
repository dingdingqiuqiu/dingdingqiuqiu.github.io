---
title: Blog多平台同步
date: 2023-11-20 22:22:17
tags: 
- 博客搭建
- Git
categories: 
- 环境搭建
- 开发环境
---
本文主要记录了博客搭建的过程以及如何利用`git`实现多平台博客同步
<!--more-->
### 当前配置

> 原理及操作详解： [先看这个和视频，整懂原理](https://blog.csdn.net/K1052176873/article/details/122879462)

> 博客搭建推荐视频：[手把手教你搭建属于自己的hexo+github博客](https://www.bilibili.com/video/BV1cW411A7Jx/?spm_id_from=333.999.0.0&vd_source=13e2561436a72606df7175a92524fa50)

> 图床推荐使用Onedrive : [图床折腾之旅](https://www.hortonyyx.com/article/image-bed)

> 博客迁移原理视频：[备份和迁移hexo博客](https://www.bilibili.com/video/BV13g41147yV/?spm_id_from=333.999.0.0&vd_source=13e2561436a72606df7175a92524fa50)

> `Windows`:`hexo`部署平台所在地，已生成一定量的博客托管在`Github`上

> `Arch`:新系统,已有`nodejs`环境。

> `Github`：博客托管平台，`main`为主分支，计划以`master`分支作为部署文件。

### 添加标签页和分类页

参考文档：

[NexT使用官方文档](https://theme-next.iissnan.com/theme-settings.html)

[NexT标签分类页使用官方文档](https://hexo.io/zh-cn/docs/front-matter.html#%E5%88%86%E7%B1%BB%E5%92%8C%E6%A0%87%E7%AD%BE)

在`next`主题的`_config.yml`配置文件中即可修改

`menu`部分把`tags`和`categories`的注释取消掉

```yaml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

在`hexo-site`页面新建`tag`页面和`categories`页面

```
hexo new page tags
```

```zsh
hexo new page categories
```

```zsh
cd your-hexo-site
```

```zsh
cd source/tags
```

```zsh
vim index.md
```

```zsh
---
title: tags
date: 2023-11-24 16:50:27
type: "tags"
---
```

```zsh
cd ../
```

```zsh
cd categories
```

```zsh
vim index.md
```

```zsh
---
title: categories
date: 2023-11-24 16:52:48
type: "categories"
---
```

###  更改新建文章模板

```zsh
cd your-hexo-site
```

```zsh
cd scaffolds   
```

```zsh
vim post.md
```

```md
---
title: {{ title }}
date: {{ date }}
tags:
categories:
---


<!--more-->
```

### 同步原理

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212674&authkey=%21ANiKrfTOh3bHFkY&width=1238&height=456" width="1238" height="" />

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
npm install -g cnpm -registry=https://registry.npm.taobao.org
```

```cmd
cnpm install hexo-deployer-git --save
```

> 别随便装插件，容易产生依赖问题，报错看着很难受

> 用`gitbash`中的`cnpm`安装，用`npm`安装也会报错

> 然而用`cnpm`安装以后迁移`hexo`检测不到安装的模块
>
> 最后还是删除此模块后又用`npm`安装解决的问题。

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

> 第一次配置时出了点问题，下面是第二次写的，首先，目录结构应该是这样的：

```zsh
# ls                                                                                                         
_config.landscape.yml  _config.yml  package.json  package-lock.json  scaffolds  source  themes
```

```zsh
pacman -S npm
```

```zsh
npm install hexo  
```

```zsh
npm install      
```

```zsh
npm install hexo-deployer-git 
```

```zsh
cnpm install -g hexo-cli    
```

> `-g`表示全局用户，这里其实应该用`npm`的，当时晚高峰，梯子效果不够好，就用了`cnpm`。

> 开梯子对安装似乎有效果，三次安装只有梯子延迟低的时候装上了

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

