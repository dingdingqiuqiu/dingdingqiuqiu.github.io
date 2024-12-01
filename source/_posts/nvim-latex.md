---
title: nvim+latex
mathjax: true
date: 2024-11-01 16:43:31
tags:
categories:
---

由于计网研讨报告的需要，安装laxtex环境写下论文。

<!--more-->

> 环境: WSL2-ubuntu22
>
> nvim: 10.1

### ubuntu安装Latex

```bash
sudo apt-get install texlive-full
```

> Pregenerating ConTeXt MarkIV format. This may take some time...
>
> 会遇到上面这句话，一直按<enter>即可跳过，bug

### 安装字体

> 这一步可以跳过，如果没有字体缺失的话
>
> 前两步没用，最后两个命令大大有用
>
> 缺字体可以像机器学习画图时那样安装

安装推荐字体

```bash
sudo apt-get install texlive-fonts-recommended
```

安装扩展字体

```bash
sudo apt-get install texlive-fonts-extra
```

更新字体映射

```bash
sudo updmap-sys
```

下面两个命令解决了问题

```bash
sudo apt-get install fonts-noto-cjk
```

```bash
sudo apt-get install fonts-wqy-microhei fonts-wqy-zenhei
```

### windows安装`Sioyek`pdf阅读器

sioyek官网：https://sioyek.info

下载安装完成后将路径加入`Windows`中的`Path`环境变量。

```bash
D:\1soft\sioyek-release-windows\sioyek-release-windows
```

补充：应将其当加入`.zshrc`中的环境变量

```bash
export PATH=$PATH:/mnt/d/1soft/sioyek-release-windows/sioyek-release-windows
```

### vim导入相关插件

`lazy-vim`插件管理器放插件的地方放入下列内容即可

```lua
  -- latex配置
{
  "lervag/vimtex",
  lazy = false,  -- we don't want to lazy load VimTeX
  -- tag = "v2.15", -- uncomment to pin to a specific release
  init = function()
    vim.g.tex_flavor = 'latex'
    -- 报错筛选
    vim.g.vimtex_quickfix_open_on_warning = 0
    vim.g.vimtex_quickfix_ignore_filters = {
      'Underfull \\hbox',
      'Overfull \\hbox',
      'LaTeX Warning: .\\+ float specifier changed to',
      'LaTeX hooks Warning',
      'Package siunitx Warning: Detected the "physics" package:',
      'Package hyperref Warning: Token not allowed in a PDF string',
      'Package fontspec Warning: Font "FandolSong-Regular" does not contain requested Script "CJK".',
    }
    -- vim.g.vimtex_view_general_viewer = 'zathura'
    -- vim.g.vimtex_view_method = 'zathura'
    -- vim.g.vimtex_view_enable_reverse_search = 1
    -- vim.g.vimtex_compiler_progname = 'nvr'
    -- VimTeX configuration goes here, e.g.
    vim.g.vimtex_view_method = 'sioyek'
    vim.g.vimtex_view_sioyek_exe = 'sioyek.exe'
    -- vim.g.vimtex_callback_progpath = 'wsl nvim'
  end
},
  "SirVer/ultisnips",
  "honza/vim-snippets",
  "sillybun/zyt-snippet",
```

### latexmkrc编译选项设置

位置在`~/.latexmkrc`,键入以下内容即可。(以下内容主要针对中文大学学位毕业论文，请按需更改)

```
# .latexmkrc
# 其他自定义选项
$pdflatex = 'xelatex';
$synctex = 1;
```

### 跳转功能

正向跳转：

在`vim`中按住\再按l再按v,跳转pdf

反向跳转：

在pdf中选中内容，跳转tex代码对应位置

> 注意tex文件不要放mnt下

### 跳转功能实现(nvim-remote)

> 该方法较为繁琐，为初级探索时使用，在此仅做记录，不推荐使用。请跳转下文`VimtexInverseSearch`方法实现

更改`/home/ubuntu22/.local/share/nvim/lazy/vimtex/autoload/vimtex/view`下的`sioyek.vim`文件中的`s:viewer._start(outfile)`函数，使得反向搜索使用我们自定义的脚本:

```lua
function! s:viewer._start(outfile) dict abort " {{{1
  " Update file path for Windows+cygwin
  let l:file = executable('cygpath')
        \ ? join(vimtex#jobs#capture('cygpath -aw "' . a:outfile . '"'), '')
        \ : a:outfile
"
"   let l:cmd  = g:vimtex_view_sioyek_exe
"         \ . ' ' . g:vimtex_view_sioyek_options
"         \ . ' --inverse-search "' . s:inverse_search_cmd
"         \ .   ' -c \"VimtexInverseSearch %2 ''%1''\""'
"         \ . ' --forward-search-file ' . vimtex#util#shellescape(expand('%:p'))
"         \ . ' --forward-search-line ' . line('.')
"         \ . ' ' . vimtex#util#shellescape(l:file)
"   " 添加这一行来打印执行的命令
"   echom "Executing command: " . l:cmd
"   " Start the view process
"   " NB: Use vimtex#jobs#start to ensure it runs in the background
"   let self.job = vimtex#jobs#start(l:cmd, {'detached': v:true})
"   let self.cmd_start = l:cmd
" endfunction
" 更改
  let l:cmd  = g:vimtex_view_sioyek_exe
        \ . ' ' . g:vimtex_view_sioyek_options
        \ . ' --inverse-search "' . 'NvimJump.bat %2 '
        \ . vimtex#util#shellescape(expand('%:p')) . ' %1"'
        \ . ' --forward-search-file ' . vimtex#util#shellescape(expand('%:p'))
        \ . ' --forward-search-line ' . line('.')
        \ . ' ' . vimtex#util#shellescape(l:file)
  " 添加这一行来打印执行的命令
  " echom "Executing command: " . l:cmd
  " Start the view process
  " NB: Use vimtex#jobs#start to ensure it runs in the background
  let self.job = vimtex#jobs#start(l:cmd, {'detached': v:true})
  let self.cmd_start = l:cmd
endfunction

```

我们这里在反向搜索时调用`Windows`下的`NvimJump.bat`来进行exe与wsl中的nvim的通信。`NvimJump.bat`内容如下:

```bat
@echo off
rem 获取参数
set LINE=%1
set FILE=%2

rem 使用 WSL 执行 nvr 命令
wsl  ~/.local/bin/nvr --servername localhost:6197 +%LINE% %FILE%
```

我们将`NvimJump.bat`添加进环境变量`path`,使得任何一个exe都可以与之通信。

在`bat`文件中，我们使用了`nvr`命令，该命令可以从终端控制一个nvim进程,安装过程如下：

```
pip3 install neovim-remote
```

我们在启动nvim时指定端口号为上文端口号即可,如下:

```zsh
nvim --listen localhost:6197 Bachelor-template.tex
```

### 跳转功能实现(VimtexInverseSearch)

我们查看`vimtex/autoload/vimtex/view`源码：发现`BUG`实际上是由于`Windows`下的`%1`参数无法被正确解析引起的，只要令

```lua
   let l:cmd  = g:vimtex_view_sioyek_exe
         \ . ' ' . g:vimtex_view_sioyek_options
         \ . ' --inverse-search "' . s:inverse_search_cmd
         \ .   ' -c \"VimtexInverseSearch %2 ''%1''\""'
         \ . ' --forward-search-file ' . vimtex#util#shellescape(expand('%:p'))
         \ . ' --forward-search-line ' . line('.')
         \ . ' ' . vimtex#util#shellescape(l:file)
```

为下面这些部分，实际上还是能继续使用`VimtexInverseSearch`功能的，注意这里错误的`%1`不能删除，如果删除，`%2`自动变成`%1`,我们将`%1`直接拼接在正确命令最后，不影响正确结果。

```lua
  let l:cmd  = g:vimtex_view_sioyek_exe
        \ . ' ' . g:vimtex_view_sioyek_options
        \ . ' --inverse-search "' . 'wsl nvim'
        \ . ' --headless -c \"VimtexInverseSearch %2 ' . vimtex#util#shellescape(expand('%:p')) . '\" %1"' 
        \ . ' --forward-search-file ' . vimtex#util#shellescape(expand('%:p'))
        \ . ' --forward-search-line ' . line('.')
        \ . ' ' . vimtex#util#shellescape(l:file)
```



