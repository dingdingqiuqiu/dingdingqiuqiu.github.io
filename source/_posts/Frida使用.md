---
title: Frida使用
date: 2023-11-18 22:19:17
tags: 
- 安卓逆向
categories: 
- Reserve
- Android
---
本文主要讲解自动化逆向工具`Frida`的使用，长期更新，计划采用理论加实战。
<!--more-->
### Frida使用

c:\users\lenovo\appdata\local\pip\cache\wheels\fb\7a\50\0944b1c7a77e594e0a66e9a1f6925dd6b262887f31358fe26a

```cmd
PS C:\Users\lenovo\Desktop> pip install frida-tools
Collecting frida-tools
  Downloading frida-tools-12.3.0.tar.gz (200 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 200.5/200.5 kB 158.2 kB/s eta 0:00:00
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Preparing metadata (pyproject.toml) ... done
Collecting colorama<1.0.0,>=0.2.7 (from frida-tools)
  Downloading colorama-0.4.6-py2.py3-none-any.whl (25 kB)
Collecting frida<17.0.0,>=16.0.9 (from frida-tools)
  Obtaining dependency information for frida<17.0.0,>=16.0.9 from https://files.pythonhosted.org/packages/53/ab/8351fd5bb7c6879e3c47c8f7d23be757d43b0585660ded476d9c9e0f1d89/frida-16.1.6-cp37-abi3-win_amd64.whl.metadata
  Downloading frida-16.1.6-cp37-abi3-win_amd64.whl.metadata (2.1 kB)
Collecting prompt-toolkit<4.0.0,>=2.0.0 (from frida-tools)
  Obtaining dependency information for prompt-toolkit<4.0.0,>=2.0.0 from https://files.pythonhosted.org/packages/1f/9d/be9b01085bbd67a71c4b6aa02518fade8104e7a2224e5de5e947811d7176/prompt_toolkit-3.0.41-py3-none-any.whl.metadata
  Downloading prompt_toolkit-3.0.41-py3-none-any.whl.metadata (6.5 kB)
Collecting pygments<3.0.0,>=2.0.2 (from frida-tools)
  Obtaining dependency information for pygments<3.0.0,>=2.0.2 from https://files.pythonhosted.org/packages/43/88/29adf0b44ba6ac85045e63734ae0997d3c58d8b1a91c914d240828d0d73d/Pygments-2.16.1-py3-none-any.whl.metadata
  Downloading Pygments-2.16.1-py3-none-any.whl.metadata (2.5 kB)
Collecting wcwidth (from prompt-toolkit<4.0.0,>=2.0.0->frida-tools)
  Obtaining dependency information for wcwidth from https://files.pythonhosted.org/packages/cd/af/fb045bb3d3daedf28e1bedd771674f73de9e06664a48b1579e14d4120158/wcwidth-0.2.10-py2.py3-none-any.whl.metadata
  Downloading wcwidth-0.2.10-py2.py3-none-any.whl.metadata (14 kB)
Downloading frida-16.1.6-cp37-abi3-win_amd64.whl (32.6 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 32.6/32.6 MB 218.2 kB/s eta 0:00:00
Downloading prompt_toolkit-3.0.41-py3-none-any.whl (385 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 385.5/385.5 kB 300.2 kB/s eta 0:00:00
Downloading Pygments-2.16.1-py3-none-any.whl (1.2 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.2/1.2 MB 217.3 kB/s eta 0:00:00
Downloading wcwidth-0.2.10-py2.py3-none-any.whl (105 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 105.2/105.2 kB 1.2 MB/s eta 0:00:00
Building wheels for collected packages: frida-tools
  Building wheel for frida-tools (pyproject.toml) ... done
  Created wheel for frida-tools: filename=frida_tools-12.3.0-py3-none-any.whl size=209506 sha256=4a80e4fa6d17f8c097e0b880f90cab15b4f2565be290ec3afbbb81168413eb95
  Stored in directory: c:\users\lenovo\appdata\local\pip\cache\wheels\fb\7a\50\0944b1c7a77e594e0a66e9a1f6925dd6b262887f31358fe26a
Successfully built frida-tools
Installing collected packages: wcwidth, pygments, prompt-toolkit, frida, colorama, frida-tools
Successfully installed colorama-0.4.6 frida-16.1.6 frida-tools-12.3.0 prompt-toolkit-3.0.41 pygments-2.16.1 wcwidth-0.2.10

[notice] A new release of pip is available: 23.2.1 -> 23.3.1
[notice] To update, run: python.exe -m pip install --upgrade pip
```

