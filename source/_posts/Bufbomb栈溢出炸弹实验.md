---
title: Bufbomb栈溢出炸弹实验
date: 2023-12-15 13:22:47
tags: 
- gdb
- asm
- CSAPP
categories: 
- [OS,asm]
- [Pwn,StackOverflow]
- [Book,CSAPP]
---

本文是对计算机系统基础第三次实验的复现，以此为基础讲解栈溢出的相关知识。

<!--more-->

### 基本逻辑逆向

`main`函数

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212861&authkey=%21ANiKrfTOh3bHFkY&width=812&height=668" width="812" height=" " />

这里比较引人注目的是`signal`函数，先执行了三个该函数，又引入标准输入的文件描述符为`infile`,以下链接比较深入讲解了`signal`函数，读者可详细了解。

[C library function - signal()](https://www.tutorialspoint.com/c_standard_library/c_function_signal.htm)

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <signal.h>

void sighandler(int);

int main () {
   signal(SIGINT, sighandler);

   while(1) {
      printf("Going to sleep for a second...\n");
      sleep(1); 
   }
   return(0);
}

void sighandler(int signum) {
   printf("Caught signal %d, coming out...\n", signum);
   exit(1);
}
```

以下是一个可能的输出

```c
Going to sleep for a second...
Going to sleep for a second...
Going to sleep for a second...
Going to sleep for a second...
Going to sleep for a second...
Caught signal 2, coming out...
```

总之，这段利用`sign`函数来实现了一些违规输入的提示。包括段溢出，进程繁忙和错误指令。例如下面是段溢出错误的提示。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212863&authkey=%21ANiKrfTOh3bHFkY&width=1489&height=660" width="1489" height="" />

接下来，程序会去走一个循环。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212865&authkey=%21ANiKrfTOh3bHFkY&width=1479&height=660" width="1479" height="" />

[`getopt() `function in C to parse command line arguments](https://www.tutorialspoint.com/getopt-function-in-c-to-parse-command-line-arguments)

`getopt()`函数原型：

```c
getopt(int argc, char *const argv[], const char *optstring)
```

- 如果选项带有一个值，那么该值将由` optarg` 指向

- 当没有更多选项可以处理时，它将返回-1
- 返回“?”表明这是一个无法识别的选项，它将其存储到 `optopt` 中。

- 有时某些选项需要一些值，如果选项存在但值不存在，那么它也会返回“？”。
- 我们可以使用 ‘:’ 作为 `optstring` 的第一个字符，这样，如果没有给出值，它将返回 ‘:’ 而不是 ‘?’。

```c
#include <stdio.h>
#include <unistd.h>

main(int argc, char *argv[]) {
   int option;
   // put ':' at the starting of the string so compiler can distinguish between '?' and ':'
   while((option = getopt(argc, argv, ":if:lrx")) != -1){ //get option from the getopt() method
      switch(option){
         //For option i, r, l, print that these are options
         case 'i':
         case 'l':
         case 'r':
            printf("Given Option: %c\n", option);
            break;
         case 'f': //here f is used for some file name
            printf("Given File: %s\n", optarg);
            break;
         case ':':
            printf("option needs a value\n");
            break;
         case '?': //used for some unknown options
            printf("unknown option: %c\n", optopt);
            break;
      }
   }
   for(; optind < argc; optind++){ //when some extra arguments are passed
      printf("Given extra arguments: %s\n", argv[optind]);
   }
}
```

以下是输出

```c
Given Option: i
Given File: test_file.c
Given Option: l
Given Option: r
Given extra arguments: hello
```

因此，很容易为这段循环设置下面这段注释

```c
while ( 1 ) // 无限循环
  {
    v4 = getopt(argc, (char *const *)argv, "gsnhu:"); // 解析命令行参数
    if ( v4 == -1 ) // 如果没有更多的参数
      break; // 退出循环
    switch ( v4 ) // 根据参数类型进行处理
    {
      case 'g': // 如果参数是 'g'
        autograde = 1; // 设置自动评分标志
        break;
      case 'h': // 如果参数是 'h'
        usage(); // 调用 usage 函数
      case 'n': // 如果参数是 'n'
        v10 = 1; // 设置 v10 标志
        v3 = 5; // 设置 v3 的值为 5
        break;
      case 's': // 如果参数是 's'
        puts("This is a quiet bomb. Ignoring -s flag."); // 输出一条消息
        notify = 0; // 设置通知标志
        break;
      case 'u': // 如果参数是 'u'
        userid = __strdup(optarg); // 复制用户 ID
        cookie = gencookie(userid); // 生成 cookie
        break;
      default: // 如果参数不是上述任何一种
        usage(); // 调用 usage 函数
    }
  }
```

```c
void __usercall __noreturn usage(const char *a1@<eax>)
{
  __printf_chk(1, "Usage: %s -u <userid> [-nsh]\n", a1);
  puts("  -u <userid> User ID");
  puts("  -n          Nitro mode");
  puts("  -s          Submit your solution to the grading server");
  puts("  -h          Print help information");
  exit(0);
}
```

最后一段，要求必须有`userid`,即执行`bufbomb`程序的时候必须添加`-u`参数。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212867&authkey=%21ANiKrfTOh3bHFkY&width=1100&height=802" width="1100" height=" " />

后面初始化`bomb`炸弹，打印`Userid`和`Cookie`。重点看下下面这段的汇编，`cookie`放`eax`寄存器以后，放到栈顶，作为`seed`执行`_srandom`函数和`_random`函数，该函数的返回值与`0xFF0`按位与运算后将结果加上256，比较符合C伪代码，并将结果放到`esp+18`的内存地址，后面将`4`和`edi`里存的数据作为参数入栈，查看`edi(v3)`的引用信息，发现默认为1，在传入`-n`参数时修改为5。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212869&authkey=%21ANiKrfTOh3bHFkY&width=862&height=677" width="862" height=" " />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212871&authkey=%21ANiKrfTOh3bHFkY&width=1030&height=287" width="1030" height="" />

使用`_calloc`函数分配好堆空间以后，把分配空间的内存地址放到`eax`寄存器中,并把堆空间中首元素置零，`ebx`寄存器赋1，跳转到`loc_80491BB`。后面分析下来基本和C伪代码保持一致。重点在于`-n`参数会把`v10(默认为0)`参数改为`1`,会把`v3（默认为1）`参数改为5，从而影响到分配到分配到的`v5`的大小，这里我将其改名为`arry`，为了便于理解，将其他参数也改名。

```c
launcher(launcher_flag, arry[j] + ran_cookie);
```

这是修改后的各个参数的名字，后面我们重点分析`launcher`函数即可。看下`launcher`函数的C风格的伪代码

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212873&authkey=%21ANiKrfTOh3bHFkY&width=1230&height=419" width="1230" height="" />

可以看到，`launcher_flag`实际上是开启`nitro`这一关的全局变量，`arry[j] + ran_cookie`随机数当作全局偏移量量出现。

```c
int __cdecl launcher(int a1, int a2) // 定义一个名为 launcher 的函数，它接受两个整数参数 a1 和 a2
{
  int v3; // 在栈上定义一个整数变量 v3 [esp+0h] [ebp-28h] BYREF

  global_nitro = a1; // 将 a1 的值赋给全局变量 global_nitro
  global_offset = a2; // 将 a2 的值赋给全局变量 global_offset

  // 使用 mmap 函数尝试在内存中预留一块空间，大小为 0x100000 字节，权限为 7（即可读、可写、可执行），并将其映射到进程的地址空间
  // 如果映射失败（即 mmap 的返回值不等于预期的地址 reserved），则向标准错误输出流 stderr 写入错误信息，并退出程序
  if ( mmap(&reserved, 0x100000u, 7, 306, 0, 0) != &reserved )
  {
    fwrite("Internal error.  Couldn't use mmap. Try different value for START_ADDR\n", 1u, 0x47u, stderr);
    exit(1);
  }

  stack_top = (int)&unk_55685FF8; // 将一个未知的内存地址赋给全局变量 stack_top
  global_save_stack = (int)&v3; // 将 v3 的地址赋给全局变量 global_save_stack

  launch(global_nitro, global_offset); // 调用名为 launch 的函数，参数为 global_nitro 和 global_offset

  return munmap(&reserved, 0x100000u); // 使用 munmap 函数取消之前通过 mmap 函数预留的内存空间，并返回 munmap 的结果
}
```

重点看下`mmap`分配的空间，应该注意到，`(int)&unk_55685FF8`实际上是`mmap()`函数分配的内存空间的`reserved`的最后一段。下面是程序头表（Program Header Table，PHT）的条目，它是在可执行和链接格式（ELF）文件中找到的。这个条目提供了加载（LOAD）段到内存中的必要信息。注意这里的 `_reserved` 物理地址这刚好是`0x5586000`,分配的权限为`6`（写和读），大小是`0x100000`。

[mmap函数说明文档](https://www.ibm.com/docs/en/zos/2.4.0?topic=functions-mmap-map-pages-memor)

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212881&authkey=%21ANiKrfTOh3bHFkY&width=1454&height=837" width="1454" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212878&authkey=%21ANiKrfTOh3bHFkY&width=1454&height=837" width="1454" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212879&authkey=%21ANiKrfTOh3bHFkY&width=1454&height=837" width="1454" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212881&authkey=%21ANiKrfTOh3bHFkY&width=1454&height=837" width="1454" height="" />

有关信息与上图所示，重点是对一些变量的赋值和分配堆空间。重点函数是`launch`,看下C风格的伪代码。下面是入栈信息，可以看到参数并未放栈上，而是放在了`eax`和`edx`寄存器中了。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212884&authkey=%21ANiKrfTOh3bHFkY&width=1540&height=871" width="1540" height="" />

看下函数本体。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212885&authkey=%21ANiKrfTOh3bHFkY&width=1454&height=837" width="1454" height="" />

C伪代码

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212887&authkey=%21ANiKrfTOh3bHFkY&width=1025&height=554" width="1025" height="" />

```c
unsigned int __usercall launch@<eax>(int global_nitro@<eax>, int global_offset@<edx>)
{
  void *v3; // esp，栈指针
  int v5; // [esp+10h] [ebp-58h] BYREF，局部变量v5
  unsigned int v6; // [esp+5Ch] [ebp-Ch]，局部变量v6
  int savedregs; // [esp+68h] [ebp+0h] BYREF，保存寄存器的值

  v6 = __readgsdword(0x14u); // 读取线程局部存储（Thread Local Storage，TLS）中的值
  v3 = alloca((((unsigned __int16)&savedregs - 76) & 0x3FF0) + global_offset + 15); // 分配一块内存，大小取决于savedregs的地址、global_offset和15
  memset(&v5, 244, ((unsigned __int16)&savedregs - 76) & 0x3FF0); // 将v5的内存区域设置为244
  __printf_chk(1, "Type string:"); // 打印字符串"Type string:"
  if ( global_nitro ) // 如果global_nitro为真
    testn(); // 调用函数testn
  else
    test(); // 否则调用函数test
  if ( !success ) // 如果success为假
  {
    puts("Better luck next time"); // 打印字符串"Better luck next time"
    success = 0; // 将success设置为0
  }
  return __readgsdword(0x14u) ^ v6; // 返回TLS中的值与v6的异或结果
}
```

进去`testn()`函数和`test()`函数看一下，大体可以分辨这两个函数为漏洞函数。`testn()`需要`-n`参数触发，`test()`函数不需要。大体知道这些就够了，具体的漏洞原理流程在每个部分具体说明。

### Smoke

可以看到，`Gets`函数最多能读取1024个字节，而数组大小仅仅为40个字节，因此，可以传入44个字节装满数组并覆盖`rbp`,4个字节覆盖返回地址，使得跳转到指定的返回地址，本题要求是跳转到`Smoke`函数，仅仅跳转过去就完成了目标。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212889&authkey=%21ANiKrfTOh3bHFkY&width=859&height=759" width="859" height="" />

原理主要是因为`leave`指令相当于

```asm
mov esp, ebp
pop ebp
```

`ret`指令相当于

```asm
pop eip
```

> 注意：这里的`eip`是不能直接操作的，所以上述代码只是为了解释`ret`的功能，并不能直接在代码中使用。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212891&authkey=%21ANiKrfTOh3bHFkY&width=1225&height=676" width="1225" height="" />

返回点在如图所示的高亮部分。

### Fizz

本题还要求`cookie`要与栈上一个值相等。

这是修改后的各个参数的名字，后面我们重点分析`launcher`函数即可。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212873&authkey=%21ANiKrfTOh3bHFkY&width=1230&height=419" width="1230" height="" />

这段除了`launch`以外都是分配内存与变量赋值的操作，重点看下`launch`函数，逻辑也很简单，开`-n`参数执行`testn`，不开`-n`执行`test`函数

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212887&authkey=%21ANiKrfTOh3bHFkY&width=1025&height=554" width="1025" height="" />

### 核心原理

`leave`指令

```asm
push esp,ebp
pop  ebp
```

`ret`指令

```asm
pop eip
```

### Smoke

> 函数在执行`getbuf`后不返回1，而是转向`Smoke`函数

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212896&authkey=%21ANiKrfTOh3bHFkY&width=824&height=344" width="824" height="" />

容易知道，`Gets`函数最多能写入1024个字节，而`v1`仅仅开辟了40（0x28）个字节的空间。因此，输入可以用44个字节覆盖整个`v1`和`ebp`，再用4个字节覆盖返回地址，使函数跳转到`Smoke`执行。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212898&authkey=%21ANiKrfTOh3bHFkY&width=735&height=189" width="735" height="" />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212900&authkey=%21ANiKrfTOh3bHFkY&width=654&height=703" width="654" height="" />

实操：生成`cookie`

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212902&authkey=%21ANiKrfTOh3bHFkY&width=556&height=101" width="556" height="" />

二进制文件`Smkoe`

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212904&authkey=%21ANiKrfTOh3bHFkY&width=839&height=156" width="839" height="" />

验证

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212906&authkey=%21ANiKrfTOh3bHFkY&width=553&height=206" width="553" height="" />

### Fizz

跳转思路与`Smoke`一致，不过这里还需要与栈上数据比较一下

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212907&authkey=%21ANiKrfTOh3bHFkY&width=774&height=297" width="774" height="" />

那就在后面再加上几位`cookie`即可

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212909&authkey=%21ANiKrfTOh3bHFkY&width=835&height=176" width="835" height="" />

执行结果

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212911&authkey=%21ANiKrfTOh3bHFkY&width=633&height=216" width="633" height="" />

### Bang

要求与全局变量进行比较，修改下全局变量

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212913&authkey=%21ANiKrfTOh3bHFkY&width=881&height=342" width="881" height="" />

在跳转`bang`之前需要执行的汇编指令如下：

```asm
push 0x3962b26d,%eax
mov  %eax,0x0804d100
ret 
```

### Nitro

通过第一阶段的基本逻辑分析容易知道：只有在执行`-n`程序的时候添加`-n`参数，该阶段才会被开启。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212922&authkey=%21ANiKrfTOh3bHFkY&width=917&height=421" width="917" height="" />

这里`testn`函数如果想正常返回，需要满足两个条件

- 栈帧不被破坏
- `getbufn`函数返回`cookie`

我们现在来寻找漏洞点来注入我们的攻击指令。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212924&authkey=%21ANiKrfTOh3bHFkY&width=693&height=208" width="693" height="" />

我们前面分析了`Gets`函数的实现，容易知道，这里是存在栈溢出漏洞的。依照前面的思路，我们用520个字符填满`v1`数组，用4个字符覆盖`ebp`，用四个字符覆盖`getbufn`的返回地址。由前面的基本逻辑逆向，我们知道，该函数实际上需要执行5次，每次执行时的栈状态不一致，因此，我们选择使用`nop`指令来填充输入中除了攻击指令其他位置，以此保证只要返回地址跳到任意一个`nop`指令上，程序都会执行我们的攻击指令。

以上的分析结束以后，是时候来生成攻击指令了。

先写出汇编代码

```asm
lea 0x28(%ebp),%esp
mov cookie,%eax
push 返回地址
ret
```

