---
title: Neovim使用
date: 2023-11-23 23:05:50
tags: 
- vim
categories: 
- 环境搭建
- 开发环境
---
本文是`vim/nvim`的使用及配置攻略。
<!--more-->

### 2024.9.13更新

> `neovim`配置文件在`~/.config/nvim`中，感觉现在`neovim`更对胃口，我的`nvim/vim`配置：
>
> https://github.com/dingdingqiuqiu/vimrc

## 基本操作

### 普通模式

> 默认打开文件进入普通模式,多用于浏览文件和粘贴复制

`hjkl`用来移动光标

`4k`向下跳4行

`w`跳转到下一个单词的开头

`b`跳转到前一个单词的开头

`gg`回到文档最上方

`G`回到文档最下方

`Ctrl u`向上翻页

`Ctrl d`向下翻页

`fx`将光标移动到离你最近的‘x'位置

`y`复制

注：`vim`在执行`yank`后并不默认把内容送到系统剪切板`"+y`才是将内容送到系统剪切板。

**vim寄存器**

无论是vim内部抑或外部的复制（[y]ank）、删除（[d]elete）、粘贴（[p]ut），在vim中都是借助registers（寄存器）实现的，vim共有9类寄存器：

| 名称                                   | 标识             | 说明                                                         |
| -------------------------------------- | ---------------- | ------------------------------------------------------------ |
| 无名（unnamed）寄存器                  | ""               | 缓存最后一次操作内容；                                       |
| 数字（numbered）寄存器                 | "0 - "9          | 缓存最近操作内容，复制与删除有别；                           |
| 行内删除（small delete）寄存器         | "-               | 缓存行内删除内容；                                           |
| 具名（named）寄存器                    | "a - "z或"A - "Z | 指定时可用；                                                 |
| 只读（read-only）寄存器                | ":, "., "%, "#   | 分别缓存最近命令、最近插入文本、当前文件名、当前交替文件名； |
| 表达式（expression）寄存器             | "=               | 只读，用于执行表达式命令；                                   |
| 选择及拖拽（selection and drop）寄存器 | "*, "+, "~       | 存取GUI选择文本，可用于与外部应用交互，使用前提为系统剪切板（clipboard）可用； |
| 黑洞（black hole）寄存器               | "_               | 不缓存操作内容（干净删除）；                                 |
| 模式寄存器（last search pattern）      | "/               | 缓存最近的搜索模式。                                         |

上面的说明为简要概述，并不完全准确，详细说明须参考手册：:help copy-move

使用下面这条命令查看`vim`是否能写入`clipbloard`

```zsh
vim --version|grep clipboard
```

```zsh
### 回显，这里`+`表示支持
+clipboard         +keymap            +printer           +vertsplit
+ex_extra          +mouse_netterm     +syntax            +xterm_clipboard
```

可能是装了[wl-clipboard](https://archlinux.org/packages/?name=wl-clipboard),我记得`kali`里是不支持的。

> 具体参见这篇：[Archwiki_Neovim](https://wiki.archlinuxcn.org/wiki/Neovim)

> `yaw`---> yank all word
>
> `y4j`---> yank 下面四行内容
>
> `yfr`---> yank 到r为止的内容

参考文档：[vim复制粘贴（与系统剪切板相关内容）](http://xstarcd.github.io/wiki/vim/vimcopy.html)

`p`粘贴

> 类似的，`"+p`是将系统剪切板内容剪切出来

`d`删除

> `dj`----> delete 当前行和下一行内容
>
> `d8j`----> delete 下八行内容

`u`撤销

### 命令行模式

> 普通模式输入`:`进入命令行模式

### 编辑模式

> 普通模式输入**i**，当前光标的前一个开始输入

> 普通模式输入**a**,当前光标的后一个开始输入

> 普通模式输入**A**,当前行的开头开始输入

>  普通模式输入**I**,当前行结尾开始输入

> `caw`删除这个单词，并进入输入模式
>
> `cc`删除该行，并进入输入模式
>
> `c4j`删除下四行，并进入输入模式

### 可视化模式

> 普通模式输入`v`进入可视模式

### 配置

#### vim-plug下载

商店：[vimawesome](https://vimawesome.com)

推荐使用`VimPlug`管理插件

[vim-plug安装配置（github项目地址）](https://github.com/junegunn/vim-plug)

我们这里使用`Vim-plug`管理插件,先下载`vim-plug`

```zsh
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### vim插件下载

[Archwiki-Vim](https://wiki.archlinuxcn.org/wiki/Vim)

[Archwiki-Nvim](https://wiki.archlinuxcn.org/wiki/Neovim)

> vim全局配置文件：`/etc/vimrc`
>
> vim插件下载到：`~/.vim/autoload`
>
> nvim全局配置文件：`/etc/xdg/nvim/sysinit.vim`
>
> nvim插件下载到：`~/.local/share/nvim/site/autoload/`

将`/etc/vimrc`配置`cp`到`/etc/xdg/nvim/sysinit.vim`后，需要把插件也从`~/.vim/autoload`文件夹`cp`到`~/.local/share/nvim/site/autoload/`。本文主要讲解`vim`的插件配置，`nvim`配置可自行迁移。

> nvim迁移后属于能用，但每次使用都有警告的存在，所以还是推荐`vim`

```zsh
## neovim配置时，官网给的这条命令跑不起来，加`--insecurity`参数也不行
## 多方面考虑，还是推荐vim
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
```

修改配置文件

```zsh
vim /etc/vimrc
```

```shell
call plug#begin()
Plug 'scrooloose/nerdtree'
call plug#end()
```

开始安装

```vim
:source %
```

```vim
:PlugInstall
```

再次进入，输入

```zsh
:NERDTree
```

即可看到文件目录

```json
" All system-wide defaults are set in $VIMRUNTIME/archlinux.vim (usually just
" /usr/share/vim/vimfiles/archlinux.vim) and sourced by the call to :runtime
" you can find below.  If you wish to change any of those settings, you should
" do it in this file (/etc/vimrc), since archlinux.vim will be overwritten
" everytime an upgrade of the vim packages is performed.  It is recommended to
" make changes after sourcing archlinux.vim since it alters the value of the
" 'compatible' option.

" This line should not be removed as it ensures that various options are
" properly set to work with the Vim-related packages.
runtime! archlinux.vim

" If you prefer the old-style vim functionalty, add 'runtime! vimrc_example.vim'
" Or better yet, read /usr/share/vim/vim80/vimrc_example.vim or the vim manual
" and configure vim to your own liking!

" do not load defaults if ~/.vimrc is missing
"let skip_defaults_vim=1

"""""""""""""""""""""""""""""""""""""""""""基本设置"""""""""""""""""""""""""""""""""""""""""""""
filetype on	"开启文件类型侦测
filetype indent on	"适应不同语言的缩进
syntax enable	"开启语法高亮功能
syntax on 	"允许使用用户配色

"""""""""""""""""""""""""""""""""""""""""""快捷键设置""""""""""""""""""""""""""""""""""""""""""
map <F3> :NERDTreeToggle<CR>    
inoremap jk <Esc>

"""""""""""""""""""""""""""""""""""""""""""显示设置"""""""""""""""""""""""""""""""""""""""""""""
set laststatus=2        	"总是显示状态栏
set ruler               	"显示光标位置
set number              	"显示行号
set cursorline          	"高亮显示当前行
set cursorcolumn            "高亮显示当前列
set hlsearch                " 高亮搜索结果
exec "nohlsearch"
set incsearch               "边输边高亮
set ignorecase              "搜索时忽略大小写
set smartcase

set relativenumber          "其他行显示相对行号
"set scrolloff=5            "垂直滚动时光标距底部位置


"""""""""""""""""""""""""""""""""""""""""""编码设置"""""""""""""""""""""""""""""""""""""""""""""
set fileencodings=utf-8,gb2312,gbk,gb18030,cp936    " 检测文件编码,将fileencoding设置为最终编码
set fileencoding=utf-8                              " 保存时的文件编码
set termencoding=utf-8                              " 终端的输出字符编码
set encoding=utf-8                                  " VIM打开文件使用的内部编码


"""""""""""""""""""""""""""""""""""""""""""编辑设置"""""""""""""""""""""""""""""""""""""""""""""
set expandtab   	"扩展制表符为空格
set tabstop=4   	"制表符占空格数
set softtabstop=4	"将连续数量的空格视为一个制表符
set shiftwidth=4	"自动缩进所使用的空格数
"set textwidth=79	"编辑器每行字符数
set wrap            "设置自动折行
set linebreak       "防止单词内部折行
set wrapmargin=5      "指定折行处与右边缘空格数
set autoindent  	"打开自动缩进
set wildmenu    	"vim命令自动补全


call plug#begin('~/.vim/plugged')
"需要配置的插件都放在begin和end之间

Plug 'scrooloose/nerdcommenter' "多行注释
Plug 'jiangmiao/auto-pairs'     "括号、引号自动补全
Plug 'scrooloose/nerdtree' 	"树形目录
Plug 'Yggdroot/indentLine'      "自动缩进插件
Plug 'vim-airline/vim-airline'  "状态栏主题
Plug 'vim-scripts/Solarized'    "主题
Plug 'honza/vim-snippets'       "代码片段补全
Plug 'SirVer/ultisnips'

"Plug 'mhinz/vim-startify'         "vim开始界面最近文件
"Plug 'connorholyday/vim-snazzy'   "主题方案
"Plug 'tpope/vim-commentary'       "代码注释
"Plug 'ryanoasis/vim-devicons'     "文件图标
"Plug 'Lokaltog/vim-powerline'     "状态栏主题

call plug#end()
```

#### 插件使用

##### `NERDTree`

NERDTree是一个非常实用的Vim插件，它提供了一种方便的方式来浏览文件系统并打开文件。以下是一些常用的NERDTree快捷键：

- `ctrl + w + h`：光标focus左侧树形目录
- `ctrl + w + l`：光标focus右侧文件显示窗口
- `ctrl + w + w`：光标自动在左右侧窗口切换
- `ctrl + w + r`：移动当前窗口的布局位置
- `o`：在已有窗口中打开文件、目录或书签，并跳到该窗口
- `go`：在已有窗口中打开文件、目录或书签，但不跳到该窗口
- `t`：在新Tab中打开选中文件/书签，并跳到新Tab
- `T`：在新Tab中打开选中文件/书签，但不跳到新Tab
- `i`：split一个新窗口打开选中文件，并跳到该窗口
- `gi`：split一个新窗口打开选中文件，但不跳到该窗口
- `s`：vsplit一个新窗口打开选中文件，并跳到该窗口
- `gs`：vsplit一个新窗口打开选中文件，但不跳到该窗口
- `!`：执行当前文件
- `O`：递归打开选中结点下的所有目录
- `x`：合拢选中结点的父目录
- `X`：递归合拢选中结点下的所有目录
- `e`：Edit the current dif
- `D`：删除当前书签
- `P`：跳到根结点
- `p`：跳到父结点
- `K`：跳到当前目录下同级的第一个结点
- `J`：跳到当前目录下同级的最后一个结点
- `k`：跳到当前目录下同级的前一个结点
- `j`：跳到当前目录下同级的后一个结点
- `C`：将选中目录或选中文件的父目录设为根结点
- `u`：将当前根结点的父目录设为根目录，并变成合拢原根结点
- `U`：将当前根结点的父目录设为根目录，但保持展开原根结点
- `r`：递归刷新选中目录
- `R`：递归刷新根结点
- `m`：显示文件系统菜单
- `cd`：将CWD设为选中目录
- `I`：切换是否显示隐藏文件
- `f`：切换是否使用文件过滤器
- `F`：切换是否显示文件
- `B`：切换是否显示书签
- `q`：关闭NerdTree窗口
- `?`：切换是否显示Quick Help

参考文档：[常用 NERDTree 快捷键](https://www.cnblogs.com/qiumingcheng/p/6275510.html)

删除开头数字及空格
```
:%s/^[0-9 ]\+//
```

