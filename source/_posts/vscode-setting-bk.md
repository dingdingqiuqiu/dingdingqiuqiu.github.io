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

   <img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VVX1YyaE5zX1oxUHJuamtHTzRhaXRZQm0yR2t3LTd0VDBRRUVGYmxGb0Z6U3c_ZT15dHY4VFo.png" width="1315" height=" " />

3. `vscode`下载`c/cpp`插件

4. `ctrl-shift-p`，选中第一个`C/C++:Edit Configurations(UI)`

   <img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VZMW9rb0NxdjB4RXYwY3AyVVlVNG80QkpvRlVTTDExZ1ZXc2pHdWdNd3lxRFE_ZT1hVHhaM0k.png" width="1579" height=" " />5.配置信息如下：

   <img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VUZ2ZHT0VCVXZ0Q21KMVdHZm5NUS0wQm1DQUxiY0tEdHVkdng0M2xvcHBDV2c_ZT0xTHhVTXU.png" width="1579" height=" " />

   其实不用改啥东西了，几乎所有选项在配置好环境变量后vscode都能自动识别，这里主要做对照用。关键是要下载`mingw64`,不要下载任何其他乱七八糟的。

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

#### 2.keybinding.json

`ctrl-shift-p`,打开箭头指的这个`Preferences:Open Keyboard Shortcuts(JSON)`即可。

<img src="https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VSeHJLS0gtX210T29DcnFUX1NYUW0wQlhoLVNLOW5XSVdFZ0lwS1QyWTg3TlE_ZT1MV0dMTnI.png" width="1092" height=" " />

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

