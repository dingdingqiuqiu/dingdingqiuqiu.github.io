---
title: vim-tmux-navigator配置使用
date: 2024-09-16 02:25:31
tags: vim,tmux，English
categories:
---
本文主要是对`vim-tmux-navigator`插件项目主页的汉化，主要为了练习英文能力。
<!--more-->
原文链接：
[vim-tmux-navigator插件github原文](https://github.com/christoomey/vim-tmux-navigator)

Vim Tmux Navigator
==================

navigator航海家

This plugin is a repackaging of Mislav Marohnić's tmux-navigator configuration described in this gist. When combined with a set of tmux key bindings, the plugin will allow you to navigate seamlessly between vim and tmux splits using a consistent set of hotkeys.

combined 联合
a set of 一组
bindings 绑定
seam接缝 seamless无缝的
consistent 一致的


这个插件是概要中描述的Mislav Marohnić的tmux-navigator配置的重打包。当与一组tmux键绑定结合使用时，该插件将允许您使用一组一致的热键在vim和tmux分割之间无缝导航。

NOTE: This requires tmux v1.8 or higher.

注意:这个插件需要tmux版本在v1.8以上
### 用法
This plugin provides the following mappings which allow you to move between Vim panes and tmux splits seamlessly.
这个插件提供以下按键映射，允许你在vim的panes（窗格）和tmux的splits（分割）间无缝切换

<ctrl-h> => Left
<ctrl-j> => Down
<ctrl-k> => Up
<ctrl-l> => Right
<ctrl-\\> => Previous split（前一个分割）

Note - you don't need to use your tmux prefix key sequence before using the mappings.
prefix前缀
注意，在使用这个映射之前你无需使用tmux的前缀键序列

If you want to use alternate key mappings, see the configuration section below.
如果你想使用可选的映射，看下面的配置那一段

## 安装

### Vim

If you don't have a preferred installation method, I recommend using Vundle. Assuming you have Vundle installed and configured, the following steps will install the plugin:
如果你没有一个特别偏爱的安装方式，我推荐你使用Vundle。假定你已经安装了Vundle并且已经配置好了，下面这些步骤可以安装这个插件

Add the following line to your ~/.vimrc file

添加下面这些行到你的vimrc文件
```
Plugin 'christoomey/vim-tmux-navigator'
```
然后运行
```
PluginInstall
```
If you are using Vim 8+, you don't need any plugin manager. Simply clone this repository inside ~/.vim/pack/plugin/start/ directory and restart Vim.
如果你使用Vim8＋，你不需要任何插件管理器.只需要克隆这个仓库到`~/.vim/pack/plugin/start`目录然后重启Vim即可
```
git clone git@github.com:christoomey/vim-tmux-navigator.git ~/.vim/pack/plugins/start/vim-tmux-navigator
```
### Lazy.nvim
If you are using lazy.nvim. Add the following plugin to your configuration.
如果你使用lazy.nvim.添加下面的插件到你的配置中
```
{
  "christoomey/vim-tmux-navigator",
  cmd = {
    "TmuxNavigateLeft",
    "TmuxNavigateDown",
    "TmuxNavigateUp",
    "TmuxNavigateRight",
    "TmuxNavigatePrevious",
  },
  keys = {
    { "<c-h>", "<cmd><C-U>TmuxNavigateLeft<cr>" },
    { "<c-j>", "<cmd><C-U>TmuxNavigateDown<cr>" },
    { "<c-k>", "<cmd><C-U>TmuxNavigateUp<cr>" },
    { "<c-l>", "<cmd><C-U>TmuxNavigateRight<cr>" },
    { "<c-\\>", "<cmd><C-U>TmuxNavigatePrevious<cr>" },
  },
}
```
Then, restart Neovim and lazy.nvim will automatically install the plugin and configure the keybindings.
然后，重启Neovim，lazy.nvim会自动安装这个插件并且配置键位绑定
## tmux
To configure the tmux side of this customization there are two options:

customization 定制

配置该定制的tmux端有两个选择:

### Add a snippet

snippet 片段

Add the following to your ~/.tmux.conf file:
添加下面这些到你的`~/.tmux.conf`文件
```
# Smart pane switching with awareness of Vim splits.
# See: https://github.com/christoomey/vim-tmux-navigator
is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
    | grep -iqE '^[^TXZ ]+ +(\\S+\\/)?g?(view|l?n?vim?x?|fzf)(diff)?$'"
bind-key -n 'C-h' if-shell "$is_vim" 'send-keys C-h'  'select-pane -L'
bind-key -n 'C-j' if-shell "$is_vim" 'send-keys C-j'  'select-pane -D'
bind-key -n 'C-k' if-shell "$is_vim" 'send-keys C-k'  'select-pane -U'
bind-key -n 'C-l' if-shell "$is_vim" 'send-keys C-l'  'select-pane -R'
tmux_version='$(tmux -V | sed -En "s/^tmux ([0-9]+(.[0-9]+)?).*/\1/p")'
if-shell -b '[ "$(echo "$tmux_version < 3.0" | bc)" = 1 ]' \
    "bind-key -n 'C-\\' if-shell \"$is_vim\" 'send-keys C-\\'  'select-pane -l'"
if-shell -b '[ "$(echo "$tmux_version >= 3.0" | bc)" = 1 ]' \
    "bind-key -n 'C-\\' if-shell \"$is_vim\" 'send-keys C-\\\\'  'select-pane -l'"

bind-key -T copy-mode-vi 'C-h' select-pane -L
bind-key -T copy-mode-vi 'C-j' select-pane -D
bind-key -T copy-mode-vi 'C-k' select-pane -U
bind-key -T copy-mode-vi 'C-l' select-pane -R
bind-key -T copy-mode-vi 'C-\' select-pane -l
```
### TPM

> 感觉不如第一种直接在`~/.tmux.config`里添加

If you prefer, you can use the Tmux Plugin Manager (TPM) instead of copying the snippet. When using TPM, add the following lines to your ~/.tmux.conf:
如果你更喜欢，你可以使用Tmux插件管理器（TPM）代替复制这个片段。当你使用TPM时，添加下面这几行到你的`~/.tmux.conf`

```
set -g @plugin 'christoomey/vim-tmux-navigator'
```

To set a different key-binding, use the plugin configuration settings (remember to update your vim config accordingly). Multiple key bindings are possible, use a space to separate.

为了设置不同的键位绑定，使用插件的配置设置（因此记得更新你的vim配置)。使用空格分割可以实现多个键位绑定。

不要忘记运行`tmp`

```
run '~/.tmux/plugins/tpm/tpm'
```

Thanks to Christopher Sexton who provided the updated tmux configuration in [this blog post](http://www.codeography.com/2013/06/19/navigating-vim-and-tmux-splits).

感谢Christopher Sexton在[这篇博客](http://www.codeography.com/2013/06/19/navigating-vim-and-tmux-splits)里提供的更新过的tmux配置。

**翻译至此，已经足够形成个人完整配置**

**如果想定制键位，看原文的Configuration部分**
