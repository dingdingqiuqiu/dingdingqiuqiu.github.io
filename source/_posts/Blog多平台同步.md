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
mathjax: true
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

### 后续添加的配置

#### 数学公式渲染

##### 第零步：探索

>  参考[github-hexo渲染数学公式](https://myblackboxrecorder.com/use-math-in-hexo/)，第0步的内容是根据别的博客进行的尝试，但这个引擎并没有生效，仅做记录用，从第一步往后的步骤有效。

> 以下命令执行均需在`dingdingqiuqiu.github.io\`下，因为`npm`需要安装包到`dingdingqiuqiu.github.io\node_modules`下

npm设置代理下载(7890是clash的代理端口,走clash代理)

```
npm config set proxy=http://127.0.0.1:7890
```

卸载原有渲染引擎：

```shell
npm uninstall hexo-renderer-marked
```

下载新的渲染引擎

```shell
npm install hexo-renderer-pandoc 
```

然后在 `dingdingqiuqiu.github.io/themes/hexo-theme-next-7.8.0/_config.yml` 中将 `mathjax` 的 `enable` 打开。

```
math:
  ...
  mathjax:
    enable: true
```

##### 第一步：安装MathJax

MathJax就是我们用来渲染数学公式的js引擎。

不过在这之前，我们还需要卸载自带的hexo-math以避免冲突。

```shell
npm uninstall hexo-math --save
```

```shell
npm install hexo-renderer-mathjax --save
```

##### 第二步：更新MathJax的CDN链接

由于MathJax官方cdn已停止维护，其他cdn也有如不稳定，必须翻墙等问题，我找到了一个可以使用的cdn地址。

以下命令显示版本：

```shell
npm show mathjax version
# 3.2.2
```

打开`dingdingqiuqiu.github.io/node_modules/hexo-renderer-mathjax/mathjax.html`

将最后一行cdn地址改为（版本可以根据实际情况修改，我根据上述命令得到版本是3.2.2）

```
<script src="//cdn.bootcss.com/mathjax/3.2.2/MathJax.js?config=TeX-MML-AM_CHTML"></script>
```

##### 第三步：更换默认渲染引擎

Hexo默认的渲染引擎hexo-renderer-marked对MathJax的支持很不好，我们修改为kramed引擎

```shell
npm uninstall hexo-renderer-marked --save
```

```shell
npm install hexo-renderer-kramed --save
```

##### 第四步：更改转义规则

> 转义规则原博客作者是错的，这里给出正确的。

因为 hexo 默认的转义规则会将一些字符进行转义，比如 `_` 转为 `<em>` ，所以我们需要对默认的规则进行修改。
首先， 打开`dingdingqiuqiu.github.io/node_modules/kramed/lib/rules/inline.js`

把：

```
escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
```

更改为：

```
escape: /^\\([`*\[\]()#$+\-.!_>])/,
```

把：

```
em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

更改为：

```
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

##### 第五步：使用hexo-filter-mathjax

对部分支持`MathJax`的主题来说，只需在主题配置文件将相关配置项开启即可使用`MathJax`。但对很多主题，如我使用的`CleanBlog`主题来说，没有这样的功能。有一个工具可以帮助我们：[hexo-filter-mathjax](https://github.com/next-theme/hexo-filter-mathjax)。

安装：

```shell
npm install hexo-filter-mathjax --save
```

对需要开启的博文，在其Front-Matter处增加mathjax: true这一行即可，如：

```
---
title: On the Electrodynamics of Moving Bodies
categories: Physics
date: 1905-06-30 12:00:00
mathjax: true
---
```

如果你还想配一些个性化需求，可以在`hexo-filter-mathjax`的教程中查看。

这样就大功告成了，写`MarkDown`的时候正常写数学公式，就可以在生成的博客中展示。

##### 第六步：修复无法渲染块级公式的BUG

> 当前配置渲染行内公式有效，对于块级公式，仍无法渲染，更换`hexo-renderer-marked`版本即可。
>
> 参考：[hexo latex 换行 多行公式 终极解决方案](https://blog.csdn.net/weixin_44634312/article/details/120197899)

更换版本：

```shell
cd blog
```

```shell
npm uninstall hexo-renderer-marked --save
```

```shell
npm install hexo-renderer-marked@1.0.0 --save
```

编辑`dingdingqiuqiu.github.io\node_modules\marked\lib\marked.js`

539行

```
escape: /^\\([!"#$%&'()*+,\-./:;<=>?@\[\]\\^_`{|}~])/,
改成
escape: /^\\([!"#$&'()*+,\-./:;<=>?@\[\]^_`|~])/,
```

564行

```
inline._escapes = /\\([!"#$%&'()*+,\-./:;<=>?@\[\]\\^_`{|}~])/g;
改成
inline._escapes = /\\([!"#$&'()*+,\-./:;<=>?@\[\]^_`|~])/g;
```

#### 中文目录跳转

- 在next github 上已经提出了该问题并给出了[解决方案](https://www.zywvvd.com/pages/go.html?goUrl=https%3A%2F%2Fgithub.com%2Ftheme-next%2Fhexo-theme-next%2Fpull%2F1540%2Ffiles)
- 修改`dingdingqiuqiu.github.io\themes\hexo-theme-next-7.8.0\source\js\utils.js`中的以下函数

```javascript
registerSidebarTOC: function() {
  const navItems = document.querySelectorAll('.post-toc li');
  const sections = [...navItems].map(element => {
    var link = element.querySelector('a.nav-link');
 var target = document.getElementById(decodeURI(link.getAttribute('href')).replace('#', ''));
    // TOC item animation navigate.
    link.addEventListener('click', event => {
      event.preventDefault();
      //var target = document.getElementById(event.currentTarget.getAttribute('href').replace('#', ''));
      var offset = target.getBoundingClientRect().top + window.scrollY;
      window.anime({
        targets  : document.scrollingElement,
        duration : 500,
        easing   : 'linear',
        scrollTop: offset + 10
      });
    });
    //return document.getElementById(link.getAttribute('href').replace('#', ''));
 return target;
  });
```

#### 站内搜索

> 参考这篇:[hexoNext主题添加站内搜索](https://yashuning.github.io/2018/06/29/hexo-Next-%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%90%9C%E7%B4%A2%E5%8A%9F%E8%83%BD/)

##### 插件安装

博客根目录下执行以下代码安装插件

```shell
 npm install hexo-generator-searchdb --save
```

##### 配置博客

安装完成，编辑博客配置文件：`_config.yml`

```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```

##### 配置主题

Next 主题自带搜索设置，编辑主题配置文件：`_config.yml`

找到文件中 Local search 的相关配置，设为 `true`

```
# Local search
local_search:
  enable: true
```

hexo 重新部署

### BUG:hexo d失败

梯子换成TUN模式重新尝试即可。
