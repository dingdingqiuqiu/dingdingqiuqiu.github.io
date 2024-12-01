---
title: OS-进程实验
mathjax: true
date: 2024-10-31 21:19:43
tags:
categories: 操作系统
---

本文基于操作系统父子进程关系，进程间信号量机制的同步和模拟临界资源访问三个实验记录进程学习过程中的问题。

<!--more-->

### fork详解

进程都是由其他进程创建出来的，每个进程都有自己的PID（进程标识号），在 Linux 系统的进程之间存在一个继承关系，所有的进程都是 init 进程（1号进程）的后代。

在 Linux 中所有的进程都通过 task_struct 描述，里面通过 parent 和 children 字段维护一个父子进程树的链表结构。而 fork 函数是创建子进程的一种方法，它是一个系统调用函数，所以在看 fork 系统调用之前我们先来看看 system_call。

系统调用处理函数 system_call 与 int 0x80 中断描述符表挂接。 system_call 是整个操作系统中系统调用软中断的总入口。所有用户程序使用系统调用，产生 int 0x80 软中断后，操作系统都是通过这个总入口找到具体的系统调用函数。

系统调用函数是操作系统对用户程序的基本支持。在操作系统中，像类似读盘、创建子进程之类的事物需要通过系统调用实现。系统调用被调用后会触发 int 0x80 软中断，然后由用户态切换到内核态（从用户进程的3特权级翻转到内核的0特权级），通过 IDT 找到系统调用端口，调用具体的系统调用函数来处理事物，处理完毕之后再由 iret 指令回到用户态继续执行原来的逻辑。

​                               ![image-20241101162405206](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VWTUpvN3hSYURCSm5IdFlpSTdWMkRVQmh0OWRxdzFpaVBZN1Y2Nnp0TmZMcEE_ZT1Wb1hkVnE.png)

fork 函数由于也是系统调用的函数之一，所以也是通过 int 0x80 软中断来进行触发的。在触发 int 0x80 软中断后会切换到内核态，找到 sys_call_table 中根据 fork 的 index（也就是2） 找到对应的函数：

 ![image-20241101162434098](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VhY2xaRHhmQU5wSGtFVUNZQi16MGhjQlktRW5GRG51U0RYR3NRWlo3X0JjOUE_ZT1IM3VCazE.png)

然后拿到对应的 C 函数 sys_fork。因为会变中对应 C 的函数名在前面多加一个下划线，所以会跳转到 _sys_fork 处执行。

在 _sys_fork 中首先会调用 find_empty_process 申请一个空闲位置并获取一个新的进程号 pid。空闲位置由 task[64] 这个数组决定，也就是说最多只能同时 64 个进程同时在跑，并用全局变量 last_pid 来存放系统自开机以来累计的进程数，如果有空闲位置，那么 ++last_pid 作为新进程的**进程号**，在 task[64] 中找到的空闲位置的 index 作为**任务号**。

_sys_fork 接下来调用 copy_process 进行进程复制：

1. 将 task_struct 复制给子进程，task_struct 是用来定义进程结构体，里面有关于进程所有信息；
2. 随后对复制来的进程结构内容进行一些修改和初始化赋0。比方说状态、进程号、父进程号、运行时间等，还有一些统计信息的初始化，其余大部分保持不变；
3. 然后会调用 copy_mem 复制进程的页表，但是由于Linux系统采用了写时复制(copy     on write)技术，因此这里仅为新进程设置自己的页目录表项和页表项，而没有实际为新进程分配物理内存页面，此时新进程与其父进程共享所有物理内存页面。
4. 最后 GDT 表中设置子进程的 TSS（Task     State Segment） 段和 LDT（Local     Descriptor Table） 段描述符项，将子进程号返回。其中 TSS 段是用来存储描述进程相关的信息，比方一些寄存器、当前的特权级别等；

 ![image-20241101162451650](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VldEdYS0JqV2hGS2lvaXlGaU90Ulg0QnFkOXBWcmYweFBWXzZ2RldiU3BhUGc_ZT1LbUJhR3A.png)

需要注意的是子进程也会继承父进程的文件描述符，也就是子进程会将父进程的文件描述符表项都复制一份，也就是说如果父进程和子进程同时写一个文件的话可能产生并发写的问题，导致写入的数据错乱。

程序流程图如下：

 ![image-20241101162505750](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VXVTl2ZmZ5bW1oS3Vxd3dmTlF3SHFFQlZaSFZ1bnI1aVBablZWaUl6TmY1VXc_ZT0xNndicFA.png)

这也解释了我们在执行子进程时对父进程中数据的复制行为。

### 孤儿进程

​	linux提供了一种机制可以保证只要父进程想知道子进程结束时的状态信息， 就可以得到。这种机制就是: 在每个进程退出的时候,内核释放该进程所有的资源,包括打开的文件,占用的内存等。但是仍然为其保留一定的信息(包括进程号the process ID,退出状态the termination status of the process,运行时间the amount of CPU time taken by the process等)。直到父进程通过wait / waitpid来取时才释放。 但这样就导致了问题，**如果进程不调用wait / waitpid的话，那么保留的那段信息就不会释放，其进程号就会一直被占用，但是系统所能使用的进程号是有限的，如果大量的产生僵死进程，将因为没有可用的进程号而导致系统不能产生新的进程. 此即为僵尸进程的危害，应当避免。**

​	于是，为了避免孤儿进程退出时无法释放所占用的资源而僵死，任何孤儿进程产生时都会立即为系统进程[init](https://zh.wikipedia.org/wiki/Init)或[systemd](https://zh.wikipedia.org/wiki/Systemd)自动接收为子进程，这一过程也被称为“收养”（英语：re-parenting）。孤儿进程是没有父进程的进程，孤儿进程这个重任就落到了init进程身上，init进程就好像是一个民政局，专门负责处理孤儿进程的善后工作。每当出现一个孤儿进程的时候，内核就把孤 儿进程的父进程设置为init，而init进程会循环地wait()它的已经退出的子进程。这样，当一个孤儿进程凄凉地结束了其生命周期的时候，init进程就会代表党和政府出面处理它的一切善后工作。**因此孤儿进程并不会有什么危害。**

### 信号量机制详解

