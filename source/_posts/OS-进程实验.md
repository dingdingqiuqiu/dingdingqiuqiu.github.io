---
title: OS-进程实验
mathjax: true
date: 2024-10-31 21:19:43
tags:
categories: 操作系统
---

本文基于操作系统父子进程关系，进程间信号量机制的同步和模拟临界资源访问三个实验记录进程学习过程中的问题。

<!--more-->

### 模拟临界资源访问
```c
#include <sys/types.h>
#include <unistd.h>
#include <signal.h>
#include <stdio.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/sem.h>
#include <stdlib.h>

#define MY_SHMKEY 10071800        // 定义共享内存的键
#define MAX_BLOCK 1024            // 定义最大块数
#define MAX_CMD 8                 // 定义最大命令长度

// 共享内存结构
struct shmbuf {
    int top;                      // 栈顶指针
    int stack[MAX_BLOCK];         // 用于存放块的数组
} *shmptr, local;                 // shmptr 指向共享内存，local 用于本地存储

char cmdbuf[MAX_CMD];             // 命令缓冲区
int shmid, semid;                 // 共享内存和信号量标识符

// 函数声明
void sigend(int);
void relblock(void);
int  getblock(void);
void showhelp(void);
void showlist(void);
void getcmdline(void);

int main(void)
{
    // 创建共享内存
    if((shmid=shmget(MY_SHMKEY, sizeof(struct shmbuf), IPC_CREAT|IPC_EXCL|0666)) < 0)
    {/* 如果共享内存已存在，作为客户端操作 */
        shmid=shmget(MY_SHMKEY, sizeof(struct shmbuf), 0666);
        shmptr=(struct shmbuf *)shmat(shmid, 0, 0); // 连接到共享内存
        local.top=-1; // 初始化本地栈顶指针
        showhelp(); // 显示帮助信息
        getcmdline(); // 获取命令行输入
        while(strcmp(cmdbuf,"end\n")) // 循环直到输入"end"
        {
            if(!strcmp(cmdbuf,"get\n"))
                getblock(); // 获取块
            else if(!strcmp(cmdbuf,"rel\n"))
                relblock(); // 释放块
            else if(!strcmp(cmdbuf,"list\n"))
                showlist(); // 列出所有获取的块
            else if(!strcmp(cmdbuf,"help\n"))
                showhelp(); // 显示帮助信息
            getcmdline(); // 再次获取命令行输入
        }
    }
    else        /* 作为服务器操作 */
    {
        int i;
        shmptr=(struct shmbuf *)shmat(shmid, 0, 0); // 连接到共享内存
        signal(SIGINT, sigend); // 捕获中断信号
        signal(SIGTERM, sigend); // 捕获终止信号
        printf("NO OTHER OPERATION but press Ctrl+C or use kill to end.\n");
        shmptr->top=MAX_BLOCK-1; // 初始化栈顶指针
        for(i=0; i<MAX_BLOCK; i++)
            shmptr->stack[i]=MAX_BLOCK-i; // 初始化栈
        sleep(1000000);    /* 永久睡眠，保持服务器运行 */
    }
}

// 信号处理函数，清理共享内存和信号量
void sigend(int sig)
{
    shmctl(shmid, IPC_RMID, 0); // 删除共享内存
    semctl(semid, IPC_RMID, 0); // 删除信号量
    exit(0);
}

// 释放块的函数
void relblock(void)
{
    if(local.top<0)
    {
        printf("No block to release!");
        return; // 没有可释放的块
    }
    shmptr->top++; // 增加共享内存栈顶指针
    shmptr->stack[shmptr->top]=local.stack[local.top--]; // 释放块到共享内存
}

// 获取块的函数
int getblock(void)
{
    if(shmptr->top<0)
    {
        printf("No free block to get!");
        return 0; // 没有可获取的块
    }
    local.stack[++local.top]=shmptr->stack[shmptr->top]; // 获取块到本地
    shmptr->top--; // 减少共享内存栈顶指针
}

// 显示帮助信息
void showhelp(void)
{
    printf("\navailable COMMAND:\n\n");
    printf("help\tlist this help\n");
    printf("list\tlist all gotten block number\n");
    printf("get\tget a new block\n");
    printf("rel\trelease the last gotten block\n");
    printf("end\texit this program\n");
}

// 列出所有获取的块
void showlist(void)
{
    int i;
    printf("List all gotten block number:\n");
    for(i=0; i<=local.top; i++)
        printf("%d\t", local.stack[i]); // 打印获取的块
}

// 获取命令行输入
void getcmdline(void)
{
    printf("\n?> ");
    fgets(cmdbuf, MAX_CMD-1, stdin); // 读取命令
}
```