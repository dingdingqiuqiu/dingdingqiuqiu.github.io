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

![bufbomb_IDA_1](../../../../../OneDrive/图片/Blog/Bufbomb栈溢出炸弹实验/bufbomb_IDA_1.png)

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

![Seghandler](../../../../../OneDrive/图片/Blog/Bufbomb栈溢出炸弹实验/Seghandler.png)

接下来，程序会去走一个循环。

![loops](../../../../../OneDrive/图片/Blog/Bufbomb栈溢出炸弹实验/loops.png)

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

![IDA_3](../../../../../OneDrive/图片/Blog/Bufbomb栈溢出炸弹实验/IDA_3.png)

后面初始化`bomb`炸弹，打印`Userid`和`Cookie`。重点看下下面这段的汇编，`cookie`放`eax`寄存器以后，放到栈顶，作为`seed`执行`_srandom`函数和`_random`函数，该函数的返回值与`0xFF0`按位与运算后将结果加上256，比较符合C伪代码，并将结果放到`esp+18`的内存地址，后面将`4`和`edi`里存的数据作为参数入栈，查看`edi(v3)`的引用信息，发现默认为1，在传入`-n`参数时修改为5。

![IDA_asm](../../../../../OneDrive/图片/Blog/Bufbomb栈溢出炸弹实验/IDA_asm.png)

![val_v3](../../../../../OneDrive/图片/Blog/Bufbomb栈溢出炸弹实验/val_v3.png)

使用`_calloc`函数分配好堆空间以后，把分配空间的内存地址放到`eax`寄存器中,并把堆空间中首元素置零，`ebx`寄存器赋1，跳转到`loc_80491BB`。后面分析下来基本和C伪代码保持一致。重点在于`-n`参数会把`v10(默认为0)`参数改为`1`,会把`v3（默认为1）`参数改为5，从而影响到分配到分配到的`v5`的大小，这里我将其改名为`arry`，为了便于理解，将其他参数也改名。

```c
launcher(launcher_flag, arry[j] + ran_cookie);
```

这是修改后的各个参数的名字，后面我们重点分析`launcher`函数即可。
