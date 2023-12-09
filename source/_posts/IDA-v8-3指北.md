---
title: IDA_v8.3指北
date: 2023-12-09 12:39:14
tags: 
- IDA
categories: 
- Reserve
- Tools
- IDA
---

本文基于IDA_Pro_8.3版本，记录使用过程中的一些问题，以及一些关于IDA的基本操作

<!--more-->

## IDA配置

### 下载

[IDA_Pro_v8.3](https://od.cloudsploit.top/zh-CN/tools/IDA/8.3)

> IDA Pro 8.3(x86，x86_64)_BGSPA.zip：这是一个压缩包，里面包含了IDA Pro 8.3的安装文件和一些插件和工具。_
>
>
> IDA Pro 8.3_(x86_x64_ARM_ARM64_PPC_PPC64_MIPS)_P.Y.G_Team.zip：这也是一个压缩包，里面包含了IDA Pro 8.3的安装文件和一些插件和工具，但是支持不同的处理器架构。
>
> IDA SDK and Tools.7z：这是一个压缩包，里面包含了IDA Pro的开发工具包（SDK），可以用来创建自己的插件和加载器。
>
> ida_keygen.exe：这是一个程序，可以用来生成IDA Pro的密钥文件（ida.key），需要输入用户名和邮箱地址。
>
> IDA_Pro_8.3-incl_kg-HexRaysDec-SDK-READ_NFO-BGSPA：这是一个压缩包，里面包含了IDA Pro 8.3的安装文件、HexRaysDec插件、SDK、README.txt和BGSPA密钥文件。HexRaysDec插件可以用来解密一些使用Hex-Rays加密技术的二进制文件。README.txt说明了如何使用这个密钥文件。BGSPA密钥文件是由BGSPA团队提供的，他们开发了一些游戏相关的反汇编工具。

### Key生成与配置

这里利用网盘中下载下来的`ida_keygen.exe`程序，`win+r`输入`cmd`,进入`ida_keygen.exe`所在的文件目录，运行以下命令，生成`ida.key`文件，将`ida.key`移动到`IDA_Pro_v8.3`本体所在文件夹即可。

```c
ida_keygen.exe -v 830 -u 用户名 -e 编一个邮箱 -t 3 -s 5169> ida.key
```

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212830&authkey=%21ANiKrfTOh3bHFkY&width=1890&height=982" width="1890" height=" " />

### 快速启动

可以将IDA所在的文件路径加入环境变量`Path`。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212832&authkey=%21ANiKrfTOh3bHFkY&width=729&height=208" width="729" height=" " />

将本体路径`D:\1Reserve\IDA_Pro_v8.3\IDA Pro 8.3 (x86, x86_64)_BGSPA\IDA Pro 8.3 (x86, x86_64)`加入到`Path`即可。（双击`Path`即可编辑值），**不要**点新建，变量名和原`Path`相同会将原`Path`覆盖。此后，在`cmd`里直接输入`ida`或`ida64`即可打开软件。

## IDA-Debug

### IDA启动时提示IDApython没有imp模块

我的`python`版本是`python3.12.0`

报错信息如下：
<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212834&authkey=%21ANiKrfTOh3bHFkY&width=689&height=143" width="689" height=" " />

因为`imp `从 `Python 3.4` 之后弃用了，所以可以使用 `importlib` 代替，具体操作如下：

修改`D:\1Reserve\IDA_Pro_v8.3\IDA Pro 8.3 (x86, x86_64)_BGSPA\IDA Pro 8.3 (x86, x86_64)\python\3`下的`ida_idaapi.py`文件。共有三处需要修改的地方

首先，将第96行的`import imp`注释掉，增加第97行`import importlib`

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212836&authkey=%21ANiKrfTOh3bHFkY&width=1593&height=575" width="1593" height=" " />

其次，将`IDAPython_LoadProcMod`函数修改成下面这个样子

```python
def IDAPython_LoadProcMod(path, g, print_error=True):
    """
    加载处理器模块。
    """
    pname = g['__name__'] if g and "__name__" in g else '__main__'
    parent = sys.modules[pname]
    path_dir, path_fname = os.path.split(path)
    procmod_name = os.path.splitext(path_fname)[0]
    procobj = None
    PY_COMPILE_ERR = None
 
    try:
        spec = importlib.util.spec_from_file_location(procmod_name, path)
        procmod = importlib.util.module_from_spec(spec)
        spec.loader.exec_module(procmod)
 
        if parent:
            setattr(parent, procmod_name, procmod)
 
            # 从父模块导出属性到处理器模块
            parent_attrs = getattr(parent, '__all__', (attr for attr in dir(parent) if not attr.startswith('_')))
            for pa in parent_attrs:
                setattr(procmod, pa, getattr(parent, pa))
 
        # 实例化处理器对象
        if getattr(procmod, 'PROCESSOR_ENTRY', None):
            procobj = procmod.PROCESSOR_ENTRY()
 
    except Exception as e:
        PY_COMPILE_ERR = f"{str(e)}\n{traceback.format_exc()}"
        if print_error:
            print(PY_COMPILE_ERR)
 
    return PY_COMPILE_ERR, procobj
 
```

最后，将下方的`IDAPython_UnLoadProcMod`函数修改成下面这个样子

```python
def IDAPython_UnLoadProcMod(script, g, print_error=True):
    """
    卸载处理器模块。
    """
    pname = g['__name__'] if g and "__name__" in g else '__main__'
    parent = sys.modules[pname]
 
    script_fname = os.path.split(script)[1]
    procmod_name = os.path.splitext(script_fname)[0]
 
    # 尝试从父模块的属性中移除处理器模块
    if hasattr(parent, procmod_name):
        delattr(parent, procmod_name)
 
    # 尝试从 sys.modules 中移除处理器模块
    try:
        sys.modules.pop(procmod_name, None)
    except Exception as e:
        print(f"Error unloading module {procmod_name}: {str(e)}")
 
    PY_COMPILE_ERR = None
    return PY_COMPILE_ERR
```

保存后重新打开`ida`,发现警告消失，问题解决。

参考文档:[IDA 8.3运行报错，如何解决？](https://www.52pojie.cn/thread-1862646-1-1.html)
