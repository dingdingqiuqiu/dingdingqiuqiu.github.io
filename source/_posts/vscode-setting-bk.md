---
title: vscode-setting-bk
mathjax: true
date: 2024-10-27 09:31:13
tags:
categories:
---

本文主要是对个人vs-code设置以及配置的备份，用于及其意外情况下的恢复（希望不会用到）

<!--more-->

`setting.json`和`keybinding.json`在`gitee`上也做了备份，地址：https://gitee.com/dingdingqiu/share

### CPP调试环境

1. 下载`mingw64`，解压到D盘

   访问下面这个链接得到资源：

   ```bash
   https://gitcode.com/open-source-toolkit/ee6ea/overview
   ```

2. 配置环境变量

   <img src="https://1drv.ms/f/c/fbd44a0636a4242e/UgQuJKQ2BkrUIID7aQAAAAAAANiKrfTOh3bHFkY?width=1315&height=694" width="1315" height=" " />

3. `vscode`下载`c/cpp`插件

4. `ctrl-shift-p`，选中第一个

   <img src="https://1drv.ms/f/c/fbd44a0636a4242e/UgQuJKQ2BkrUIID7aQAAAAAAANiKrfTOh3bHFkY?width=1579&height=796" width="1579" height=" " />

   5.配置信息如下：

   <img src="https://1drv.ms/f/c/fbd44a0636a4242e/UgQuJKQ2BkrUIID7aQAAAAAAANiKrfTOh3bHFkY?width=1390&height=2584" width="1390" height=" " />

### VSCode_编辑配置

#### 1.Setting.json

设置里直接搜索`setting.json`即可

```json
{
    "security.allowedUNCHosts": [
        "wsl.localhost"
    ],

    "git.openRepositoryInParentFolders": "never",
    "cmake.showOptionsMovedNotification": false,
    "code-runner.runInTerminal": true,
    "security.workspace.trust.untrustedFiles": "open",
    
    "workbench.colorTheme": "Tokyo Night Storm",
    "vim.easymotion": true,
    "vim.incsearch": true,
    "vim.useSystemClipboard": true,
    "vim.useCtrlKeys": true,
    "vim.hlsearch": true,
    "vim.leader": "<space>",
    "vim.insertModeKeyBindings": [
      {
        "before": ["j", "k"],
        "after": ["<Esc>"]
      },
      {
        "before": ["<C-j>"],
        "commands": ["workbench.action.terminal.focus"]
      },
    ],
    "vim.normalModeKeyBindingsNonRecursive": [
      {
        "before": ["<leader>","c"],
        "commands": ["workbench.action.closeActiveEditor"]
      },
      {
        "before": ["<leader>", "n", "h"],
        "commands": [":nohl"]
      },
      {
        "before": ["L"],
        "commands": [":tabnex"]
      },
      {
        "before": ["H"],
        "commands": [":tabprev"]
      },
      {
        "before": ["<leader>", "f", "o"],
        "commands": ["workbench.action.openRecent"]
      },
      {
        "before": ["<C-j>"],
        "commands": ["workbench.action.terminal.focus"]
      },
    ],

    "vim.handleKeys": {
        "<C-a>": false,
        "<C-f>": false
    },
}
```

### 2.keybinding.jsoon

`ctrl-shift-p`,打开箭头指的这个即可。

<img src="https://1drv.ms/f/c/fbd44a0636a4242e/UgQuJKQ2BkrUIID7aQAAAAAAANiKrfTOh3bHFkY?width=1092&height=614" width="1092" height=" " />

配置为：

```json
// Place your key bindings in this file to override the defaults
[
    {
        "key": "shift+alt+down",
        "command": "-editor.action.insertCursorBelow",
        "when": "editorTextFocus"
    },
    {
        "key": "shift+alt+down",
        "command": "editor.action.copyLinesDownAction",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "ctrl+shift+alt+down",
        "command": "-editor.action.copyLinesDownAction",
        "when": "editorTextFocus && !editorReadonly"
    },
    {
        "key": "ctrl+h",
        "command": "workbench.action.navigateLeft"
    },
    {
        "key": "ctrl+l",
        "command": "workbench.action.navigateRight"
    },
    {
        "key": "ctrl+k",
        "command": "workbench.action.focusActiveEditorGroup",
        "when": "terminalFocus"
    },
]
```

