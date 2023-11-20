## c语言数值常量的类型

安装`gcc`多平台编译工具

```zsh
pacman -S gcc-multilib    
```

安装32位程序所需的`libc`库

```zsh
pacman -S lib32-glibc lib32-gcc-libs
```

实验代码

```c
#include <stdio.h>

int main(){
        if(-2147483648>2147483647)
                printf("false!!!");
        else
                printf("true!!!");
        return 0;
}
```

实验结果

![2023-11-12_17-24](计算机系统基础NJU/2023-11-12_17-24.png) 

关键点在于理解计算机先不管符号，根据`2127483648`确定类型为`unsigned int`，再加上符号，对`0x80000000`进行补码转换又得到`0x80000000`，由于先前已经确定`unsigned int`类型，所以比较时，以`2147483648(0x80000000)`和`2147483647(0x7fffffff)`比较，满足大于条件，输出`false`。

参考下文：![VPS_Location_Serveimage](../../../../../OneDrive/图片/Blog/计算机系统基础NJU/VPS_Location_Serveimage.png)

[计算机系统基础读书笔记摘要](https://blog.csdn.net/gzxb1995/article/details/104334278)

