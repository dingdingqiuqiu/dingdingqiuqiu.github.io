---
title: Bomb二进制炸弹拆弹实验
date: 2023-11-21 09:33:24
tags: 
- gdb
- asm
- CSAPP
categories: 
- [OS,asm]
- [Reserve,Linux]
- [Book,CSAPP]
---
本文是对计算机系统基础第二次实验的复现，涉及`gdb`使用和汇编语言学习两个知识点。
<!--more-->

### 一些说明

1.本文图片托管在`Onedrive`上，请自备梯子，否则图片无法正常显示

2.感谢[二进制炸弹拆弹实验](https://blog.csdn.net/ailbj/article/details/134863333?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170202139316800192229870%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=170202139316800192229870&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~times_rank-1-134863333-null-null.142^v96^pc_search_result_base2&utm_term=%E4%BA%8C%E8%BF%9B%E5%88%B6%E7%82%B8%E5%BC%B9%E6%8B%86%E5%BC%B9%E5%AE%9E%E9%AA%8C&spm=1018.2226.3001.4187)对本文的引用

3.推荐使用工具`IDA`可以更方便地解决问题，本文更偏向汇编分析，建议读者自行了解`IDA`的使用

### 一些准备

一、下载
我们默认把peda、pwngdb、pwndbg都安装用户的根目录下，可以减少一些文件的改动。

root下，下载 前先进入用户根目录：

```zsh
cd ~
```

下载`Pwngdb`

```zsh
git clone https://github.com/scwuaptx/Pwngdb.git 
```

下载`pwndbg`

```zsh
git clone https://github.com/pwndbg/pwndbg
```

下载`peda`

```zsh
git clone https://github.com/longld/peda.git
```

下载`gef`

```zsh
git clone https://github.com/hugsy/gef.git
```

下载完成后目录如下所示：

```zsh
# ls
peda  pwndbg  Pwngdb gef
```

二、配置

先安装`pwndbg`

```zsh
cd ～/pwndbg
./setup.sh
```

插件配置

> 打开文件后文件内容修改如下，这里要注意`source /root/pwndbg/gdbinit.py `一定要在`source /root/Pwngdb/angelheap/gdbinit.py`前面，要不然会使用默认配置。

```zsh
vim /etc/gdb/gdbinit
```

`gdb`的全局用户配置目录在`/etc/gdb/gdbinit`，为使得`pwndbg`汇编各式为`AT&T`，在该文件中添加`set disassembly-flavor att`

`set debuginfod enabled on`是`/root/pwndbg/.gdbinit`中自带的，我给迁移过来了，还有中间的`define ......`也是自带的，使用时通过对`source /root/peda/peda.py`和`source /root/pwndbg/gdbinit.py`和`source /root/gef/gef.py`加解注释实现不同插件的使用。

```zsh
source /root/pwndbg/gdbinit.py
#source /root/peda/peda.py
#source /root/gef/gef.py
source /root/Pwngdb/pwngdb.py
source /root/Pwngdb/angelheap/gdbinit.py

define hook-run
python
import angelheap
angelheap.init_angelheap()
end
end

set debuginfod enabled on
set disassembly-flavor att
```

若想在普通用户下使用原版`gdb`，需要将第7-12行注释掉。

### phase_1

answer

```
Public speaking is very easy.
1 2 6 24 120 720
7 b 524
9 austinpowers 
/0%+-!
4 2 6 3 1 5 
1001
```

反汇编代码如下

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212733&authkey=%21ANiKrfTOh3bHFkY&width=686&height=366" width="686" height=" " />

这里的关键函数显然是<strings_not_equal>，在执行<strings_not_equal>时，两个参数入栈，经过实际测试（这里使用了gdb的一个插件pwndbg），一个参数是输入，一个参数是目标字符串，测试过程如下：

我们在输入时尝试输入字符串"11111111"时

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212728&authkey=%21ANiKrfTOh3bHFkY&width=924&height=282" width="924" height="" />

查看下栈信息，可以看到ebx+0x8的内存地址存的数据为0x804b680，根据反汇编代码，这个数据要传给eax,然后作为一个参数入栈，另一个参数是立即数0x80497c0

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212739&authkey=%21ANiKrfTOh3bHFkY&width=1170&height=128" width="1170" height=" " />

接下来打印传入的两个参数作为内存地址储存了什么字符串：

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212754&authkey=%21ANiKrfTOh3bHFkY&width=624&height=104" width="624" height=" " />

可以看到传参确实符合猜测，eax存放输入字符串的内存地址，立即数0x80497c0存放目标字符串的内存地址。

动态调试同时发现strings_not_equal函数通过比较传入字符串和目标字符串，改变eax的数值，相等eax为0,不等为1,也符合`<+28>`,`<+30>`处的判断;

我们已经知道了目标字符串是“Public speaking is very easy.”，尝试传入结果，通过检测。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212741&authkey=%21ANiKrfTOh3bHFkY&width=810&height=146" width="810" height=" " />

### phase_2

反汇编代码

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212757&authkey=%21ANiKrfTOh3bHFkY&width=620&height=728" width="620" height=" " />

注意这里`<+19>`处要读入六个数字，我们确定了字符类型为六个数字，我们这里不妨输入"1 2 3 4 5 6"，执行`<+19>`处

`<read_six_numbers>`后，栈变成了以下模样

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212730&authkey=%21ANiKrfTOh3bHFkY&width=998&height=264" width="998" height="" />

很明显`<+27>`处`  cmpl  $1, -0x18(%ebp)`是将立即数1与栈上`%ebp-0x18`地址存放的地址`0xffffcc00`指向内容（第一个数字`1`）比较，我们这里满足，后面的关键就是要过下面这一段的循环

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212759&authkey=%21ANiKrfTOh3bHFkY&width=1130&height=288" width="1130" height=" " />

这里关键的地方在与`<+46>`处的相乘操作，这一步实际上实现了`v[i] = v[i-1] * i`的效果，这里eax原本是下标`i`（因为`<+46>`处)，而`-4（%esi,%ebx,4)`实际上对应了上一个数字`v[i-1]`。两个数相乘结果放在eax里，再比较参数`v[i]`是否等于eax。根据参数1为1。我们可以构造`1 2 6 24 120 720`，尝试输入，满足题意。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212745&authkey=%21ANiKrfTOh3bHFkY&width=652&height=142" width="652" height=" " />

### phase_3

反汇编代码：

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212727&authkey=%21ANiKrfTOh3bHFkY&width=732&height=960" width="732" height=" " />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212747&authkey=%21ANiKrfTOh3bHFkY&width=640&height=668" width="640" height=" " />

首先看下`sscanf@plt   `的调用，了解到该函数的第一个参数是字符串，第二个参数是格式,同时，该函数返回匹配的参数个数。

```c
int sscanf(const char *str, const char *format, ...)
```

```c
//Example:

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main () {
   int day, year;
   char weekday[20], month[20], dtm[100];

   strcpy( dtm, "Saturday March 25 1989" );
   sscanf( dtm, "%s %s %d  %d", weekday, month, &day, &year );

   printf("%s %d, %d = %s\n", month, day, year, weekday );
    
   return(0);
}

//result
//March 25, 1989 = Saturday
```

我们关心格式，打印下格式

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212758&authkey=%21ANiKrfTOh3bHFkY&width=534&height=146" width="534" height=" " />

这里可以看到edx保存了输入字符串保存的地址。输入格式是“%d %c %d”,可以看到后面eax需要大于2，也就是说输入需要完全匹配这个格式。再往后会把栈中数据和0x7比较，看下栈上数据即可，动态调试发现这个数据就是输入‘’%d %c %d‘’中的第一个%d,至于为什么这个数据被放栈上了，实际上是在sscanf执行前的参数入栈决定的，这是入栈的参数

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212751&authkey=%21ANiKrfTOh3bHFkY&width=1278&height=182" width="1278" height=" " />

执行sscanf以后

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212770&authkey=%21ANiKrfTOh3bHFkY&width=1320&height=170" width="1320" height=" " />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212746&authkey=%21ANiKrfTOh3bHFkY&width=1192&height=122" width="1192" height=" " />

可以看到，符合预期，后面的重点放在0xffffcbfc 0xffffcc03 0xffffcc04处即可，容易发现，他们也在栈上，并且，0xffffcbfc处恰好就是要与0x7比较的栈上数据，ja是无符号大于，+240处是炸弹，也就是这里参数一小于等于7,且无符号数。后面，参数1作为偏移量，跳转到0x80497e8加偏移量乘4内存处地址存放的数据指向的指令，看下这个指令在哪个地址（这里以0作为偏移量)。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212749&authkey=%21ANiKrfTOh3bHFkY&width=358&height=372" width="358" height=" " />

很容易可以看到，这里参数1为0时，要跳转到0x8048be0,这里观察发现其实就是跳转到<+72>的位置

继续往下走

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212732&authkey=%21ANiKrfTOh3bHFkY&width=976&height=314" width="976" height=" " />

这里0x309和第二个%d比较，0x71ffcb80的低位和0x71比较，这里都是正确的，直接结束程序，进入下一阶段，当然根据偏移量的不同，该题答案不同，其他偏移量的情况大多也与该次情况类似，不多赘述。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212737&authkey=%21ANiKrfTOh3bHFkY&width=1264&height=260" width="1264" height=" " />

经过实验，该题八种不同的答案为

``` 
0 q 777
1 b 214
2 b 755
3 k 251
4 o 160
5 t 458
6 v 780
7 b 524
```

实验结果

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212721&authkey=%21ANiKrfTOh3bHFkY&width=546&height=116" width="546" height=" " />

### phase_4

反汇编代码如下

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212736&authkey=%21ANiKrfTOh3bHFkY&width=680&height=578" width="680" height=" " />

sscanf函数重合了，看下格式要求输入数字，大致看了一眼汇编，发现这个数字需要大于0,该数字同时作为参数传入fun4,fun4的返回值要是55（0x37）,所以关键就落在了fun4上

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212742&authkey=%21ANiKrfTOh3bHFkY&width=638&height=636" width="638" height=" " />

这里0x8(%ebp)位置为上一个栈帧，保存了传入参数9，也就是，当传参小于1时，直接给eax置1,返回。

否则，执行fun(n-1)+fun(n-2),明显是递归，c代码尝试逆向如下

```c
int func4(int n){
if(n <= 1)
    return 1;
else
    return func4(n-1)+func(n-2);
}
```

解密程序

```c
#include <stdio.h>

int func4(int n){
    if(n <= 1)
        return 1;
    else
        return func4(n-1)+func4(n-2);
}

int main(){
    int result = 55; // 该函数的返回值
    int n = 0;
    while(func4(n) != result){
        n++;
    }
    printf("该函数的传入值为：%d\n", n);
    return 0;
}
```

编译运行，结果为9,尝试输入9,结果正确。实验结果：

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212719&authkey=%21ANiKrfTOh3bHFkY&width=530&height=108" width="530" height=" " />

### phase_5

反汇编代码如下：

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212729&authkey=%21ANiKrfTOh3bHFkY&width=710&height=916" width="710" height=" " />

汇编信息：前面一段保证字符串需要有六个字符，到<+38>处看一眼，打印下0x804b220

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212725&authkey=%21ANiKrfTOh3bHFkY&width=912&height=82" width="912" height=" " />

容易知道，这段数组的存放地址被放入了esi寄存器，经历五轮循环，依次取出输入字符，取出字符后仅要低四位，作为数组该数组的数组下标取出字符，放入al寄存器以后转移到0xffffcc00，可以看到第一次取出字符‘O’(0x4f)，截取出0xf（15），作为下标，取出arry[15],即字符‘g’，放入0xffffcc00的位置，如此经历循环，直到0xffcc00处有六个字符。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212753&authkey=%21ANiKrfTOh3bHFkY&width=1892&height=1046" width="1892" height=" " />

后面执行strings_not_equal函数，入栈两个参数，一个是0xffffcc00，即-2(%ebp)，也即%ecx，一个是0x804980b，看下0x804980b，发现是字符串“giants”。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212720&authkey=%21ANiKrfTOh3bHFkY&width=1428&height=54" width="1428" height=" " />

该字符串中字符在开头数组中的下标依次是

```c
0xf 0x0 0x5 0xb 0xd 0x1
```

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212725&authkey=%21ANiKrfTOh3bHFkY&width=912&height=82" width="912" height=" " />

根据ASCII码表，可知

```c
0xf{'/' '?' 'O' '_' 'o'}
0x0{'0' '@' 'P' '`' 'p'}
0x5{'%' '5' 'E' 'U' 'e' 'u'}
0xb{'+' ';' 'K' '[' 'k' '{'}
0xd{'-' '=' 'M' ']' 'm' '}'}
0x2{'！' '1' 'A' 'Q' 'a' 'q'}
```

从上到下依次在大括号里随机选择一个字符组成字符串即可。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212765&authkey=%21ANiKrfTOh3bHFkY&width=1128&height=742" width="1128" height=" " />

试验结果

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212761&authkey=%21ANiKrfTOh3bHFkY&width=548&height=116" width="548" height=" " />

### phase_6

反汇编后代码如下

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212752&authkey=%21ANiKrfTOh3bHFkY&width=662&height=984" width="662" height=" " />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212767&authkey=%21ANiKrfTOh3bHFkY&width=728&height=992" width="728" height=" " />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212750&authkey=%21ANiKrfTOh3bHFkY&width=726&height=76" width="726" height=" " />

前面代码逻辑比较简单，read_six_numbers函数将尝试输入的字符“1 2 3 4 5 6”放在栈上的特定位置，以便后续使用；并将链表的第一个元素node1(0x804b26c)放栈上；同时，我们打印链表的结构，方便后续使用。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212762&authkey=%21ANiKrfTOh3bHFkY&width=814&height=356" width="814" height=" " />

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212744&authkey=%21ANiKrfTOh3bHFkY&width=790&height=290" width="790" height="" />

下一阶段是和一个循环一起使用来保证输入的每一个数均小于等于6

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212722&authkey=%21ANiKrfTOh3bHFkY&width=790&height=326" width="790" height=" " />

继续向下读，结合上面这段，发现这不但是个循环，还是个双层循环，这段需要仔细读。我会给出C风格的伪代码来方便理解。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212743&authkey=%21ANiKrfTOh3bHFkY&width=772&height=618" width="772" height=" " />

这一段的伪代码如下

```c
for(int i = 0;i <= 5;i++){
    if(v[i] > 6)
            explode_bomb();
	for(int j = i+1;j <=5;j++){
        if(v[i] == v[j])
            explode_bomb();
    }
}
```

这段保证了输入的六个数字均小于等于6,且互不相等。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212772&authkey=%21ANiKrfTOh3bHFkY&width=694&height=576" width="694" height=" " />

应该注意到，这块代码大概分成两个部分，在<+120>前，主要进行了一些栈初始化的操作，以方便接下来的使用。<+120>到<+170>是一个大循环。主要是将链表以输入数字的顺序依次放在栈空间上，核心操作步骤是<+163>,循环结束后形成的栈如下图所示。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212735&authkey=%21ANiKrfTOh3bHFkY&width=1000&height=542" width="1000" height=" " />

后面这段根据栈上链表的顺序，对原链表进行了重新指向设定。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212740&authkey=%21ANiKrfTOh3bHFkY&width=644&height=222" width="644" height="" />

这段循环执行后链表的结构如图所示

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212755&authkey=%21ANiKrfTOh3bHFkY&width=756&height=312" width="756" height=" " />

来看最后一部分

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212769&authkey=%21ANiKrfTOh3bHFkY&width=726&height=428" width="726" height=" " />

可将其分成3部分。第一部分是<+216>之前的栈/寄存器初始化，第二部分是<+216>至<+237>之间的循环体，第三部分是之后的销毁栈指令。重点在循环体处，在循环中，要求每一个链表后的元素必须小于或等于当前链表中的元素。因此，我们将初始链表进行排序，作为输入参数传入，即可拆弹成功。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212766&authkey=%21ANiKrfTOh3bHFkY&width=630&height=102" width="630" height=" " />

### secret_phase

入口寻找比较简单，注意到每次通过一个phase,都会经过一个phase_defused函数，反汇编看下，这个函数是干嘛的。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212731&authkey=%21ANiKrfTOh3bHFkY&width=732&height=818" width="732" height=" " />

<+7>处的比较要求0x804b480处的值与0x6相等。下面是通过第一阶段以后的0x804b480,经过尝试，每通过一个阶段，储存在该位置的数据加一，此处相当于记录了通过的关数，因此，只有走第六关以后的那个phase_defused函数才能进入secret_phase。实际上，incl num_input_strings在每个阶段执行前的<read_line+180>处执行，因此，每到一关，0x804b480处的数值加一。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212738&authkey=%21ANiKrfTOh3bHFkY&width=586&height=52" width="586" height=" " />

再往下走发现熟悉的sscanf函数，看下传入参数

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212768&authkey=%21ANiKrfTOh3bHFkY&width=742&height=120" width="742" height="" />

应该注意到，这里的“9 ”是第四关输入数据，检测的格式是“%d %s”,这是否提示我们：第四关的输入不止有“9”，还有一个字符串？继续向下看，要求sscanf返回值为2,也就是我们必须再输入一个字符串。注意到下面还有strings_not_equal函数，我们查看该函数的入栈参数，判断需要输入字符串的具体情况。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212764&authkey=%21ANiKrfTOh3bHFkY&width=952&height=76" width="952" height=" " />

所以，我们在第四行“9”后面输入“austinpowers”,开启secret_phase函数。看下secret_phase函数的反汇编代码

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212748&authkey=%21ANiKrfTOh3bHFkY&width=780&height=746" width="780" height="" />

汇编的逻辑并不复杂，把输入字符串转成长整型之后，该长整型数据大小必须小于或等于1001,并与n1一起传入函数fun7,要求函数fun7的返回值为7。我们重点关注fun7实现的功能。

fun7的反汇编如下

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212726&authkey=%21ANiKrfTOh3bHFkY&width=698&height=870" width="698" height=" " />

可知道，该函数实现了对二叉树的操作，先打印下传入的二叉树。我们从传入fun7的n1参数，即根节点0x804b320开始打印该二叉树。

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212771&authkey=%21ANiKrfTOh3bHFkY&width=708&height=702" width="708" height="" />

我们可以把这棵树画出来

```c
         36
    8          50
 6    22    45    107
1 7 20 35 40 47 99 1001           
```

这个阶段的汇编并不复杂，主要是根据传入数据和当前节点递归处理来寻找目标二叉树节点，并对返回值进行处理。传入数据如果大于该节点储存的值，向左寻找，向左寻找会将被递归的fun7的返回值n进行（n`*`2+1）的处理；传入数据如果小于储存的值，向右寻找，向右边寻找会将被递归的fun7的返回值n进行(n`*`2)的处理；如果传入数据等于当前节点，直接返回0。因此只能一直向右左边边寻找得到1001，倘若在107-1001之间，<+14>处不会跳转，eax会变0xffffff,也不满足题目意思，因此，只能找到1001节点本身。自然，答案是1001。我们最后来验证一下成果：

<img src="https://onedrive.live.com/embed?resid=FBD44A0636A4242E%212734&authkey=%21ANiKrfTOh3bHFkY&width=1888&height=280" width="1888" height=" " />

可以看到所有阶段，包括secret stage阶段，都已经被攻克。

### phase_6_调试记录

#### 静态分析

```asm
(gdb) disas phase_6
Dump of assembler code for function phase_6:
   ;第一部分
   0x08048d98 <+0>:     push   %ebp						;基地指针入栈
   0x08048d99 <+1>:     mov    %esp,%ebp				;栈顶栈底指针统一
   0x08048d9b <+3>:     sub    $0x4c,%esp				;开辟栈空间
   0x08048d9e <+6>:     push   %edi						;参数入栈
   0x08048d9f <+7>:     push   %esi						;参数入栈
   0x08048da0 <+8>:     push   %ebx						;参数入栈
   0x08048da1 <+9>:     mov    0x8(%ebp),%edx			;把%epd加0x8指向内容给%edx
   0x08048da4 <+12>:    movl   $0x804b26c,-0x34(%ebp)	;立即数放栈上
   0x08048dab <+19>:    add    $0xfffffff8,%esp			;由于模运算的性质，多开辟了(f-8)空间
   0x08048dae <+22>:    lea    -0x18(%ebp),%eax			;把%ebp-0x18值送入%eax(栈上地址入寄存器)
   0x08048db1 <+25>:    push   %eax						;参数入栈
   0x08048db2 <+26>:    push   %edx						;参数入栈
   0x08048db3 <+27>:    call   0x8048fd8 <read_six_numbers> ;函数调用
   
   ;第二部分
   0x08048db8 <+32>:    xor    %edi,%edi				;edi归零
   0x08048dba <+34>:    add    $0x10,%esp				;栈空间减少
   0x08048dbd <+37>:    lea    0x0(%esi),%esi			;空指令
   ;第五部分
   0x08048dc0 <+40>:    lea    -0x18(%ebp),%eax			;把%ebp-0x18值送入%eax
   0x08048dc3 <+43>:    mov    (%eax,%edi,4),%eax		;把%eax+%edi*4值指向内存地址(在栈上)送入%eax
   0x08048dc6 <+46>:    dec    %eax						;%eax减一送入%eax
   0x08048dc7 <+47>:    cmp    $0x5,%eax				;计算%eax-0x5，改变标志位
   0x08048dca <+50>:    jbe    0x8048dd1 <phase_6+57>   ;`below or equal`跳转（%eax-0x5<=0跳）
   0x08048dcc <+52>:    call   0x80494fc <explode_bomb> ;上一步不跳转就炸了
   
   ;第三部分
   0x08048dd1 <+57>:    lea    0x1(%edi),%ebx			;%edi+1放入%ebx
   0x08048dd4 <+60>:    cmp    $0x5,%ebx				;计算%ebx-0x5,改变标志位
   0x08048dd7 <+63>:    jg     0x8048dfc <phase_6+100>	;`greater`跳转(%ebx-0x5>0跳) 
   0x08048dd9 <+65>:    lea    0x0(,%edi,4),%eax		;%edi*4值送入%eax
   0x08048de0 <+72>:    mov    %eax,-0x38(%ebp)			;%eax指向的值送入栈上（%ebp-0x38）
   0x08048de3 <+75>:    lea    -0x18(%ebp),%esi			;%ebp-0x18送入%esi
   0x08048de6 <+78>:    mov    -0x38(%ebp),%edx			;%ebp-0x38指向的值送入%edx
--Type <RET> for more, q to quit, c to continue without paging--c
   0x08048de9 <+81>:    mov    (%edx,%esi,1),%eax		;%edx+%esi*1指向的值送入%eax
   0x08048dec <+84>:    cmp    (%esi,%ebx,4),%eax		;%eax值-（%esi+%ebx*4）指向的值改变标志位
   0x08048def <+87>:    jne    0x8048df6 <phase_6+94>	;`not equal`不相等跳转（...！=0跳）
   0x08048df1 <+89>:    call   0x80494fc <explode_bomb> ;上一步必须跳，不然炸弹炸了
   0x08048df6 <+94>:    inc    %ebx						;`increment`,相当于%ebx++
   0x08048df7 <+95>:    cmp    $0x5,%ebx				;%ebx-0x5,改变标志位
   0x08048dfa <+98>:    jle    0x8048de6 <phase_6+78>	;`jump if less or equal`(....<=跳)
   
   ;第四部分
   0x08048dfc <+100>:   inc    %edi						;%edi++
   0x08048dfd <+101>:   cmp    $0x5,%edi				;计算%edi-0x5，改变标志位
   0x08048e00 <+104>:   jle    0x8048dc0 <phase_6+40>	;%edi-0x5<=0跳转
   
   ;第五部分
   0x08048e02 <+106>:   xor    %edi,%edi				;%edi清零
   0x08048e04 <+108>:   lea    -0x18(%ebp),%ecx			;%ebp-0x18值送入%ecx
   0x08048e07 <+111>:   lea    -0x30(%ebp),%eax			;%ebp-0x30值送入%eax
   0x08048e0a <+114>:   mov    %eax,-0x3c(%ebp)			;%eax的值送入栈上(%ebp-0x3c的地址上)
   0x08048e0d <+117>:   lea    0x0(%esi),%esi			;空指令
   0x08048e10 <+120>:   mov    -0x34(%ebp),%esi			;%ebp-0x34指向的值送入%esi
   0x08048e13 <+123>:   mov    $0x1,%ebx				;把%ebx置1
   0x08048e18 <+128>:   lea    0x0(,%edi,4),%eax		;%edi*4的值送入%eax
   0x08048e1f <+135>:   mov    %eax,%edx				;%eax值送入%edx
   0x08048e21 <+137>:   cmp    (%eax,%ecx,1),%ebx		;%ebx-(%eax+%ecx*1)指向值改变标志位
   0x08048e24 <+140>:   jge    0x8048e38 <phase_6+160>	;(gewater or equal)大于等于跳转
   0x08048e26 <+142>:   mov    (%edx,%ecx,1),%eax		;(%edx+%ecx*1)指向的值赋给%eax
   0x08048e29 <+145>:   lea    0x0(%esi,%eiz,1),%esi	;空指令，%eiz并不存在
   0x08048e30 <+152>:   mov    0x8(%esi),%esi			;%esi+0x8指向的值赋给%esi
   0x08048e33 <+155>:   inc    %ebx						;%ebx++
   0x08048e34 <+156>:   cmp    %eax,%ebx				;%ebx-%eax
   0x08048e36 <+158>:   jl     0x8048e30 <phase_6+152>	;%ebx-%eax<0跳转
   
   ;第六部分
   0x08048e38 <+160>:   mov    -0x3c(%ebp),%edx			;(%ebp-0x3x)指向的值赋给%edx
   0x08048e3b <+163>:   mov    %esi,(%edx,%edi,4)		;%esi值赋给(%edx+%edi*4)
   0x08048e3e <+166>:   inc    %edi						;%edi++
   0x08048e3f <+167>:   cmp    $0x5,%edi				;%edi-0x5
   0x08048e42 <+170>:   jle    0x8048e10 <phase_6+120>	;`less or equal`小于或等于跳转
   
   ;第七部分
   0x08048e44 <+172>:   mov    -0x30(%ebp),%esi			;(%ebp-0x30)指向的值赋给%esi
   0x08048e47 <+175>:   mov    %esi,-0x34(%ebp)			;%esi值放栈上（%ebp-0x34）位置
   0x08048e4a <+178>:   mov    $0x1,%edi				;0x1赋给%edi
   0x08048e4f <+183>:   lea    -0x30(%ebp),%edx			;%ebp-0x30赋给%edx
   0x08048e52 <+186>:   mov    (%edx,%edi,4),%eax		;%edx+%edi*4指向地址的值赋给%eax
   0x08048e55 <+189>:   mov    %eax,0x8(%esi)			;%eax值放在0x8+%esi位置
   0x08048e58 <+192>:   mov    %eax,%esi				;%eax赋给%esi
   0x08048e5a <+194>:   inc    %edi						;%edi++
   0x08048e5b <+195>:   cmp    $0x5,%edi				;%edi-0x5改变标志位
   0x08048e5e <+198>:   jle    0x8048e52 <phase_6+186>	;`less or equal`小于等于跳
   
   ;第八部分
   0x08048e60 <+200>:   movl   $0x0,0x8(%esi)			;0放%esi+0x8位置
   0x08048e67 <+207>:   mov    -0x34(%ebp),%esi			;%ebp-0x34位置的值赋给%esi
   0x08048e6a <+210>:   xor    %edi,%edi				;edi置零
   0x08048e6c <+212>:   lea    0x0(%esi,%eiz,1),%esi	;空指令
   0x08048e70 <+216>:   mov    0x8(%esi),%edx			;%esi+0x8位置的值赋给%edx
   0x08048e73 <+219>:   mov    (%esi),%eax				;esi指向值赋给%eax
   0x08048e75 <+221>:   cmp    (%edx),%eax				;%eax-%edx值改变标志位
   0x08048e77 <+223>:   jge    0x8048e7e <phase_6+230>  ;`greater or equal`大于等于跳转
   0x08048e79 <+225>:   call   0x80494fc <explode_bomb>	;不跳就炸了
   
   ;第九部分
   0x08048e7e <+230>:   mov    0x8(%esi),%esi			;%esi+0x8指向地址的值赋给%esi
   0x08048e81 <+233>:   inc    %edi						;%edi++
   0x08048e82 <+234>:   cmp    $0x4,%edi				;%edi-0x4值改变标志位
   0x08048e85 <+237>:   jle    0x8048e70 <phase_6+216>	;`less or equal`小于等于就跳转
   
   0x08048e87 <+239>:   lea    -0x58(%ebp),%esp			;%ebp-0x58值赋给%esp
   0x08048e8a <+242>:   pop    %ebx						;栈顶放%ebx里
   0x08048e8b <+243>:   pop    %esi						;栈顶放%esi里
   0x08048e8c <+244>:   pop    %edi						;栈顶放%edi里
   0x08048e8d <+245>:   mov    %ebp,%esp				;销毁整个0x58大小的栈帧
   0x08048e8f <+247>:   pop    %ebp						;空指令
   0x08048e90 <+248>:   ret								;返回到原函数
End of assembler dump.
```

#### 动态调试

通过静态分析不难得知(`read_six_numbers`)输入需要六个数字，不妨

```zsh
vim answer.txt
```

第六行输入

```zsh
1 2 3 4 5 6
```

调试时

```gdb
r answer.txt
```

##### 第一部分

附一个静态的汇编代码

```asm
	;第一部分
   0x08048d98 <+0>:     push   %ebp						;基地指针入栈
   0x08048d99 <+1>:     mov    %esp,%ebp				;栈顶栈底指针统一
   0x08048d9b <+3>:     sub    $0x4c,%esp				;开辟栈空间
   0x08048d9e <+6>:     push   %edi						;参数入栈
   0x08048d9f <+7>:     push   %esi						;参数入栈
   0x08048da0 <+8>:     push   %ebx						;参数入栈
   0x08048da1 <+9>:     mov    0x8(%ebp),%edx			;把%epd加0x8指向内容给%edx
   0x08048da4 <+12>:    movl   $0x804b26c,-0x34(%ebp)	;立即数放栈上
   0x08048dab <+19>:    add    $0xfffffff8,%esp			;由于模运算的性质，多开辟了(f-8)空间
   0x08048dae <+22>:    lea    -0x18(%ebp),%eax			;把%ebp-0x18值送入%eax(栈上地址入寄存器)
   0x08048db1 <+25>:    push   %eax						;参数入栈
   0x08048db2 <+26>:    push   %edx						;参数入栈
   0x08048db3 <+27>:    call   0x8048fd8 <read_six_numbers> ;函数调用
```

在`read_six_numbers`前寄存器

```zsh
 EAX  0xffffcbf0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
 EBX  0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
 ECX  0xfffffff1
 EDX  0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
 EDI  0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
 ESI  0x80486e0 (_init) ◂— pushl %ebp
 EBP  0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
*ESP  0xffffcba0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
*EIP  0x8048db3 (phase_6+27) ◂— calll 0x8048fd8
```

在`read_six_numbers`后寄存器

```zsh
*EAX  0x6
 EBX  0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
*ECX  0xffffca90 —▸ 0xffffcaac ◂— 0xfbad8001
*EDX  0x0
 EDI  0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
 ESI  0x80486e0 (_init) ◂— pushl %ebp
 EBP  0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
 ESP  0xffffcba0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
*EIP  0x8048db8 (phase_6+32) ◂— xorl %edi, %edi
```

在`read_six_numbers`前栈帧

```zsh
'这段内容是执行后被删除的内容'
00:0000│ esp 0xffffcba0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
01:0004│-064 0xffffcba4 —▸ 0xffffcbf0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
02:0008│-060 0xffffcba8 ◂— 0x0
03:000c│-05c 0xffffcbac —▸ 0xf7e20e34 (_GLOBAL_OFFSET_TABLE_) ◂— decl %esp /* 'L\r"' */

'这段内容是执行前后不变的内容'
04:0010│-058 0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
05:0014│-054 0xffffcbb4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
06:0018│-050 0xffffcbb8 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
07:001c│-04c 0xffffcbbc —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
08:0020│-048 0xffffcbc0 —▸ 0x80486e0 (_init) ◂— pushl %ebp
09:0024│-044 0xffffcbc4 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
0a:0028│-040 0xffffcbc8 —▸ 0xffffcbf8 —▸ 0xffffcc18 —▸ 0xffffcc38 ◂— 0x0
0b:002c│-03c 0xffffcbcc —▸ 0x80491ea (skip+58) ◂— addl $0x10, %esp
0c:0030│-038 0xffffcbd0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
0d:0034│-034 0xffffcbd4 —▸ 0x804b26c (node1) ◂— 0xfd
0e:0038│-030 0xffffcbd8 —▸ 0x804c1a0 ◂— 0xfbad2488
0f:003c│-02c 0xffffcbdc —▸ 0xf7c56fd9 (printf+41) ◂— addl $0x1c, %esp
10:0040│-028 0xffffcbe0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
11:0044│-024 0xffffcbe4 —▸ 0x80497a0 ◂— incl %edi /* 'Good work!  On to the next...\n' */

'这段内容执行后被替换了'
12:0048│-020 0xffffcbe8 —▸ 0xffffcc04 ◂— 0x7374 /* 'ts' */
13:004c│-01c 0xffffcbec ◂— 0x0
14:0050│ eax 0xffffcbf0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
15:0054│-014 0xffffcbf4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
16:0058│-010 0xffffcbf8 —▸ 0xffffcc18 —▸ 0xffffcc38 ◂— 0x0
17:005c│-00c 0xffffcbfc —▸ 0x8049208 (read_line+12) ◂— testl %eax, %eax
18:0060│-008 0xffffcc00 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
19:0064│-004 0xffffcc04 ◂— 0x7374 /* 'ts' */
1a:0068│ ebp 0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
```

在`read_six_numbers`后栈帧

```zsh
'这段内容被删除'


'这段内容是执行前后不变的内容'
00:0000│ esp 0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
01:0004│-054 0xffffcbb4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
02:0008│-050 0xffffcbb8 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
03:000c│-04c 0xffffcbbc —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
04:0010│-048 0xffffcbc0 —▸ 0x80486e0 (_init) ◂— pushl %ebp
05:0014│-044 0xffffcbc4 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
06:0018│-040 0xffffcbc8 —▸ 0xffffcbf8 ◂— 0x3
07:001c│-03c 0xffffcbcc —▸ 0x80491ea (skip+58) ◂— addl $0x10, %esp
08:0020│-038 0xffffcbd0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
09:0024│-034 0xffffcbd4 —▸ 0x804b26c (node1) ◂— 0xfd
0a:0028│-030 0xffffcbd8 —▸ 0x804c1a0 ◂— 0xfbad2488
0b:002c│-02c 0xffffcbdc —▸ 0xf7c56fd9 (printf+41) ◂— addl $0x1c, %esp
0c:0030│-028 0xffffcbe0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
0d:0034│-024 0xffffcbe4 —▸ 0x80497a0 ◂— incl %edi /* 'Good work!  On to the next...\n' */

'这段内容是执行后被替换的内容'
0e:0038│-020 0xffffcbe8 —▸ 0xffffcc04 ◂— 0x6
0f:003c│-01c 0xffffcbec ◂— 0x0
10:0040│-018 0xffffcbf0 ◂— 0x1
11:0044│-014 0xffffcbf4 ◂— 0x2
12:0048│-010 0xffffcbf8 ◂— 0x3
13:004c│-00c 0xffffcbfc ◂— 0x4
14:0050│-008 0xffffcc00 ◂— 0x5
15:0054│-004 0xffffcc04 ◂— 0x6
16:0058│ ebp 0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
```

##### 第二部分*

附一个静态分析得到的汇编代码

```asm
   ;第二部分
   0x08048db8 <+32>:    xor    %edi,%edi				;edi归零
   0x08048dba <+34>:    add    $0x10,%esp				;栈空间减少
   0x08048dbd <+37>:    lea    0x0(%esi),%esi			;空指令
   0x08048dc0 <+40>:    lea    -0x18(%ebp),%eax			;把%ebp-0x18值送入%eax
   0x08048dc3 <+43>:    mov    (%eax,%edi,4),%eax		;把%eax+%edi*4值指向内存地址(在栈上)送入%eax
   0x08048dc6 <+46>:    dec    %eax						;%eax减一送入%eax
   0x08048dc7 <+47>:    cmp    $0x5,%eax				;计算%eax-0x5，改变标志位
   0x08048dca <+50>:    jbe    0x8048dd1 <phase_6+57>   ;`below or equal`跳转（%eax-0x5<=0跳）
   0x08048dcc <+52>:    call   0x80494fc <explode_bomb> ;上一步不跳转就炸了
```

比较关键的是走到`<+40>`位置，执行以后可以看到栈空间如下所示

```zsh
00:0000│ esp 0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
01:0004│-054 0xffffcbb4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
02:0008│-050 0xffffcbb8 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
03:000c│-04c 0xffffcbbc —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
04:0010│-048 0xffffcbc0 —▸ 0x80486e0 (_init) ◂— pushl %ebp
05:0014│-044 0xffffcbc4 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
06:0018│-040 0xffffcbc8 —▸ 0xffffcbf8 ◂— 0x3
07:001c│-03c 0xffffcbcc —▸ 0x80491ea (skip+58) ◂— addl $0x10, %esp
08:0020│-038 0xffffcbd0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
09:0024│-034 0xffffcbd4 —▸ 0x804b26c (node1) ◂— 0xfd
0a:0028│-030 0xffffcbd8 —▸ 0x804c1a0 ◂— 0xfbad2488
0b:002c│-02c 0xffffcbdc —▸ 0xf7c56fd9 (printf+41) ◂— addl $0x1c, %esp
0c:0030│-028 0xffffcbe0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
0d:0034│-024 0xffffcbe4 —▸ 0x80497a0 ◂— incl %edi /* 'Good work!  On to the next...\n' */
0e:0038│-020 0xffffcbe8 —▸ 0xffffcc04 ◂— 0x6
0f:003c│-01c 0xffffcbec ◂— 0x0
10:0040│ eax 0xffffcbf0 ◂— 0x1                 /* '这里eax存的值为0xffffcbf0,也就是第一个参数的地址' */
11:0044│-014 0xffffcbf4 ◂— 0x2
12:0048│-010 0xffffcbf8 ◂— 0x3
13:004c│-00c 0xffffcbfc ◂— 0x4
14:0050│-008 0xffffcc00 ◂— 0x5
15:0054│-004 0xffffcc04 ◂— 0x6
16:0058│ ebp 0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
```

走到`<+43>`位置，将数值取出放`eax`中，因为`edi = 0`，所以执行后，`eax = 1`,通过下图也容易知道，猜想成立。

```zsh
*EAX  0x1
 EBX  0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
 ECX  0xffffca90 —▸ 0xffffcaac ◂— 0xfbad8001
 EDX  0x0
 EDI  0x0
 ESI  0x80486e0 (_init) ◂— pushl %ebp
 EBP  0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
 ESP  0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
*EIP  0x8048dc6 (phase_6+46) ◂— decl %eax
```

后面逻辑比较简单

```zsh
   0x08048dc6 <+46>:    dec    %eax						;%eax减一送入%eax
   0x08048dc7 <+47>:    cmp    $0x5,%eax				;计算%eax-0x5，改变标志位
   0x08048dca <+50>:    jbe    0x8048dd1 <phase_6+57>   ;`below or equal`跳转（%eax-0x5<=0跳）
   0x08048dcc <+52>:    call   0x80494fc <explode_bomb> ;上一步不跳转就炸了
```

根据以上推理，也就是要满足：`第一个参数-1-5<=0`。也就是第一个参数要小于6。

##### 第三部分*

老样子，附一个静态分析的汇编代码

```asm
   0x08048dd1 <+57>:    lea    0x1(%edi),%ebx			;%edi+1放入%ebx
   0x08048dd4 <+60>:    cmp    $0x5,%ebx				;计算%ebx-0x5,改变标志位
   0x08048dd7 <+63>:    jg     0x8048dfc <phase_6+100>	;`greater`跳转(%ebx-0x5>0跳)
```

由于前面`<+32>`代码

```asm
 0x08048db8 <+32>:    xor    %edi,%edi				;edi归零
```

所以这段其实是必不会跳转的，如果跳转的话，跳转第四部分。

```asm
   0x08048dd9 <+65>:    lea    0x0(,%edi,4),%eax		;%edi*4值送入%eax
   0x08048de0 <+72>:    mov    %eax,-0x38(%ebp)			;%eax指向的值送入栈上（%ebp-0x38）
   0x08048de3 <+75>:    lea    -0x18(%ebp),%esi			;%ebp-0x18送入%esi
   0x08048de6 <+78>:    mov    -0x38(%ebp),%edx			;%ebp-0x38指向的值送入%edx
```

执行前栈帧

```zsh
00:0000│ esp 0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
01:0004│-054 0xffffcbb4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
02:0008│-050 0xffffcbb8 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
03:000c│-04c 0xffffcbbc —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
04:0010│-048 0xffffcbc0 —▸ 0x80486e0 (_init) ◂— pushl %ebp
05:0014│-044 0xffffcbc4 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
06:0018│-040 0xffffcbc8 —▸ 0xffffcbf8 ◂— 0x3
07:001c│-03c 0xffffcbcc —▸ 0x80491ea (skip+58) ◂— addl $0x10, %esp
08:0020│-038 0xffffcbd0 —▸ 0x804b810 (input_strings+400) ◂— xorl %esp, (%eax) /* 0x20322031; '1 2 3 4 5 6 ' */
09:0024│-034 0xffffcbd4 —▸ 0x804b26c (node1) ◂— 0xfd
0a:0028│-030 0xffffcbd8 —▸ 0x804c1a0 ◂— 0xfbad2488
0b:002c│-02c 0xffffcbdc —▸ 0xf7c56fd9 (printf+41) ◂— addl $0x1c, %esp
0c:0030│-028 0xffffcbe0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
0d:0034│-024 0xffffcbe4 —▸ 0x80497a0 ◂— incl %edi /* 'Good work!  On to the next...\n' */
0e:0038│-020 0xffffcbe8 —▸ 0xffffcc04 ◂— 0x6
0f:003c│-01c 0xffffcbec ◂— 0x0
10:0040│-018 0xffffcbf0 ◂— 0x1
11:0044│-014 0xffffcbf4 ◂— 0x2
12:0048│-010 0xffffcbf8 ◂— 0x3
13:004c│-00c 0xffffcbfc ◂— 0x4
14:0050│-008 0xffffcc00 ◂— 0x5
15:0054│-004 0xffffcc04 ◂— 0x6
16:0058│ ebp 0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
```

执行后栈帧

```zsh
00:0000│ esp 0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
01:0004│-054 0xffffcbb4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
02:0008│-050 0xffffcbb8 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
03:000c│-04c 0xffffcbbc —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
04:0010│-048 0xffffcbc0 —▸ 0x80486e0 (_init) ◂— pushl %ebp
05:0014│-044 0xffffcbc4 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
06:0018│-040 0xffffcbc8 —▸ 0xffffcbf8 ◂— 0x3
07:001c│-03c 0xffffcbcc —▸ 0x80491ea (skip+58) ◂— addl $0x10, %esp
08:0020│-038 0xffffcbd0 ◂— 0x0
09:0024│-034 0xffffcbd4 —▸ 0x804b26c (node1) ◂— 0xfd
0a:0028│-030 0xffffcbd8 —▸ 0x804c1a0 ◂— 0xfbad2488
0b:002c│-02c 0xffffcbdc —▸ 0xf7c56fd9 (printf+41) ◂— addl $0x1c, %esp
0c:0030│-028 0xffffcbe0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
0d:0034│-024 0xffffcbe4 —▸ 0x80497a0 ◂— incl %edi /* 'Good work!  On to the next...\n' */
0e:0038│-020 0xffffcbe8 —▸ 0xffffcc04 ◂— 0x6
0f:003c│-01c 0xffffcbec ◂— 0x0
10:0040│ esi 0xffffcbf0 ◂— 0x1
11:0044│-014 0xffffcbf4 ◂— 0x2
12:0048│-010 0xffffcbf8 ◂— 0x3
13:004c│-00c 0xffffcbfc ◂— 0x4
14:0050│-008 0xffffcc00 ◂— 0x5
15:0054│-004 0xffffcc04 ◂— 0x6
16:0058│ ebp 0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
```

继续向下执行

```zsh
   0x08048de9 <+81>:    mov    (%edx,%esi,1),%eax		;%edx+%esi*1指向的值送入%eax
   0x08048dec <+84>:    cmp    (%esi,%ebx,4),%eax		;%eax值-（%esi+%ebx*4）指向的值改变标志位
   0x08048def <+87>:    jne    0x8048df6 <phase_6+94>	;`not equal`不相等跳转（...！=0跳）
   
   0x08048df1 <+89>:    call   0x80494fc <explode_bomb> ;上一步必须跳，不然炸弹炸了
   0x08048df6 <+94>:    inc    %ebx						;`increment`,相当于%ebx++
   0x08048df7 <+95>:    cmp    $0x5,%ebx				;%ebx-0x5,改变标志位
   0x08048dfa <+98>:    jle    0x8048de6 <phase_6+78>	;`jump if less or equal`(....<=跳)
```

执行前寄存器

```c
 EAX  0x0
 EBX  0x1
 ECX  0xffffca90 —▸ 0xffffcaac ◂— 0xfbad8001
 EDX  0x0
 EDI  0x0
 ESI  0xffffcbf0 ◂— 0x1
 EBP  0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
 ESP  0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
*EIP  0x8048de9 (phase_6+81) ◂— movl (%edx, %esi), %eax
```

执行后寄存器

```c
 EAX  0x1
 EBX  0x1
 ECX  0xffffca90 —▸ 0xffffcaac ◂— 0xfbad8001
 EDX  0x0
 EDI  0x0
 ESI  0xffffcbf0 ◂— 0x1
 EBP  0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
 ESP  0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf20 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
*EIP  0x8048df6 (phase_6+94) ◂— incl %ebx
```

这里容易知道：实际上这一步是必须要跳转的，不然炸弹必炸，跳转条件是第一个参数和第二个参数不能相等，跳转以后执行

```asm
   0x08048df6 <+94>:    inc    %ebx						;`increment`,相当于%ebx++
   0x08048df7 <+95>:    cmp    $0x5,%ebx				;%ebx-0x5,改变标志位
   0x08048dfa <+98>:    jle    0x8048de6 <phase_6+78>	;`jump if less or equal`(....<=跳)
```

```c
//伪代码;%esi + %ebx * 4 是否等于 %eax(第一个参数)  
for(int %ebx = 1;%ebx <= 5;%ebx++){
    jmp <phase_6+78>
}
```

这里得到了炸弹不会爆炸的第二个条件:其余参数不能和第一个参数相等

##### 第四部分*

这段代码如下，其实很简单，就是对`%edi++`，然后将`%edi`和`5`比较，小于等于就跳转到第五部分

```asm
   0x08048dfc <+100>:   inc    %edi						;%edi++
   0x08048dfd <+101>:   cmp    $0x5,%edi				;计算%edi-0x5，改变标志位
   0x08048e00 <+104>:   jle    0x8048dc0 <phase_6+40>	;%edi-0x5<=0跳转 
```

```c
//伪代码
for(int i = 0;i <= 5;i++){
		jmp <phase_6+40>;
}
```

这一部分通过调试，又回到了最开始判断`%eax-1`是否小于等于5的地方。但此时的`%eax`变成了参数二，根据循环6次的条件，推断出炸弹不爆炸的第三个条件是：第一个条件和第二个条件在所有参数上均成立。

##### 第五部分

当`%eax`为第六个参数时，在重新执行第三个部分时，由于`<phase_6+57>`处

```asm
   0x8048dd1 <phase_6+57>     leal   1(%edi), %ebx
   0x8048dd4 <phase_6+60>     cmpl   $5, %ebx
 ► 0x8048dd7 <phase_6+63>   ✔ jg     phase_6+100                     <phase_6+100>
```

这里由于`ebx`的值大于5,，所以发生了跳转，直接跳到了`phase_6+100`的位置，不过也好理解，毕竟如果执行到最后一个参数，它必然不可能和其他参数相等，所有无需比较，直接跳转即可。来看看`phase_6+100`处汇编

```asm
   0x08048dfc <+100>:   inc    %edi						;%edi++
   0x08048dfd <+101>:   cmp    $0x5,%edi				;计算%edi-0x5，改变标志位
   0x08048e00 <+104>:   jle    0x8048dc0 <phase_6+40>	;%edi-0x5<=0跳转 
```

由于`%edi`在`inc`后为6，进行跳转，直接向下执行

```asm
   0x08048e02 <+106>:   xor    %edi,%edi				;%edi清零
   0x08048e04 <+108>:   lea    -0x18(%ebp),%ecx			;%ebp-0x18值送入%ecx
   0x08048e07 <+111>:   lea    -0x30(%ebp),%eax			;%ebp-0x30值送入%eax
   0x08048e0a <+114>:   mov    %eax,-0x3c(%ebp)			;%eax的值送入栈上(%ebp-0x3c的地址上)
   0x08048e0d <+117>:   lea    0x0(%esi),%esi			;空指令
   0x08048e10 <+120>:   mov    -0x34(%ebp),%esi			;%ebp-0x34指向的值送入%esi
   0x08048e13 <+123>:   mov    $0x1,%ebx				;把%ebx置1
   0x08048e18 <+128>:   lea    0x0(,%edi,4),%eax		;%edi*4的值送入%eax
   0x08048e1f <+135>:   mov    %eax,%edx				;%eax值送入%edx
   0x08048e21 <+137>:   cmp    (%eax,%ecx,1),%ebx		;%ebx-(%eax+%ecx*1)指向值改变标志位
   0x08048e24 <+140>:   jge    0x8048e38 <phase_6+160>	;(gewater or equal)大于等于跳转
```

动态调试，容易知道：如果1大于等于参数1,跳`<phase_6+160>`，我们这里参数一为`1`，显然符合条件跳`<phase_6+160>`

##### 第六部分

这里定义跳转到的`<phase_6+160>`为第六部分

```asm
   0x08048e38 <+160>:   mov    -0x3c(%ebp),%edx			;(%ebp-0x3x)指向的值赋给%edx
   0x08048e3b <+163>:   mov    %esi,(%edx,%edi,4)		;%esi值赋给(%edx+%edi*4)
   0x08048e3e <+166>:   inc    %edi						;%edi++
   0x08048e3f <+167>:   cmp    $0x5,%edi				;%edi-0x5
   0x08048e42 <+170>:   jle    0x8048e10 <phase_6+120>	;`less or equal`小于或等于跳转
```

这里给`%edi++`,然后和`0x5`比较，显然直接跳`<phase_6+120>`，相当于回到第五部分，执行第二个参数和`1`比较，这里显然`1`并不大于参数二，因此不执行跳转，继续执行，即下面这段会继续执行

```asm
   0x8048e13 <phase_6+123>    movl   $1, %ebx
   0x8048e18 <phase_6+128>    leal   (, %edi, 4), %eax
   0x8048e1f <phase_6+135>    movl   %eax, %edx
   0x8048e21 <phase_6+137>    cmpl   (%eax, %ecx), %ebx
   0x8048e24 <phase_6+140>    jge    phase_6+160
```

下面这块属实没啥意义（`%ebx`每次循环都要`++`,总有不满足跳转条件的一次），不满足跳转条件直接回到`<phase_6+160>`了

```asm
   0x08048e26 <+142>:   mov    (%edx,%ecx,1),%eax		;(%edx+%ecx*1)指向的值赋给%eax
   0x08048e29 <+145>:   lea    0x0(%esi,%eiz,1),%esi	;空指令，%eiz并不存在
   0x08048e30 <+152>:   mov    0x8(%esi),%esi			;%esi+0x8指向的值赋给%esi
   0x08048e33 <+155>:   inc    %ebx						;%ebx++
   0x08048e34 <+156>:   cmp    %eax,%ebx				;%ebx-%eax
   0x08048e36 <+158>:   jl     0x8048e30 <phase_6+152>	;%ebx-%eax<0跳转
```

联系上方的分析，可知在执行循环，先记录下这几个`node`，不知道有啥用

```zsh
pwndbg> p $node1
$1 = void
pwndbg> p *(int *)node1
'Cannot access memory at address 0xfd'
pwndbg> p (int)node1
$2 = 253
pwndbg> p (int)node2
$3 = 725
pwndbg> p (int)node3
$4 = 301
pwndbg> p (int)node4
$5 = 997
pwndbg> p (int)node5
$6 = 212
pwndbg> p (int)node6
$7 = 432
pwndbg> p (int)node7
No symbol "node7" in current context.
```

执行完这个循环以后的栈结构如图

```c
pwndbg> stack 23
00:0000│ esp 0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf21 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
01:0004│-054 0xffffcbb4 —▸ 0x80486e0 (_init) ◂— pushl %ebp
02:0008│-050 0xffffcbb8 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
03:000c│-04c 0xffffcbbc —▸ 0xffffccf4 —▸ 0xffffcf21 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
04:0010│-048 0xffffcbc0 —▸ 0x80486e0 (_init) ◂— pushl %ebp
05:0014│-044 0xffffcbc4 —▸ 0xf7ffcb80 (_rtld_global_ro) ◂— addb %al, (%eax)
06:0018│-040 0xffffcbc8 —▸ 0xffffcbf8 ◂— 0x3
07:001c│-03c 0xffffcbcc —▸ 0xffffcbd8 —▸ 0x804b26c (node1) ◂— 0xfd
08:0020│-038 0xffffcbd0 ◂— 0x10
09:0024│-034 0xffffcbd4 —▸ 0x804b26c (node1) ◂— 0xfd
0a:0028│ edx 0xffffcbd8 —▸ 0x804b26c (node1) ◂— 0xfd
0b:002c│-02c 0xffffcbdc —▸ 0x804b260 (node2) ◂— 0x2d5
0c:0030│-028 0xffffcbe0 —▸ 0x804b254 (node3) ◂— 0x12d
0d:0034│-024 0xffffcbe4 —▸ 0x804b248 (node4) ◂— 0x3e5
0e:0038│-020 0xffffcbe8 —▸ 0x804b23c (node5) ◂— 0xd4
0f:003c│-01c 0xffffcbec —▸ 0x804b230 (node6) ◂— 0x1b0
10:0040│ ecx 0xffffcbf0 ◂— 0x1
11:0044│-014 0xffffcbf4 ◂— 0x2
12:0048│-010 0xffffcbf8 ◂— 0x3
13:004c│-00c 0xffffcbfc ◂— 0x4
14:0050│-008 0xffffcc00 ◂— 0x5
15:0054│-004 0xffffcc04 ◂— 0x6
16:0058│ ebp 0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
```

##### 第七部分

继续向下执行

```asm
   0x08048e44 <+172>:   mov    -0x30(%ebp),%esi			;(%ebp-0x30)指向的值赋给%esi
   0x08048e47 <+175>:   mov    %esi,-0x34(%ebp)			;%esi值放栈上（%ebp-0x34）位置
   0x08048e4a <+178>:   mov    $0x1,%edi				;0x1赋给%edi
   0x08048e4f <+183>:   lea    -0x30(%ebp),%edx			;%ebp-0x30赋给%edx
   0x08048e52 <+186>:   mov    (%edx,%edi,4),%eax		;%edx+%edi*4指向地址的值赋给%eax
   0x08048e55 <+189>:   mov    %eax,0x8(%esi)			;%eax值放在0x8+%esi位置
   0x08048e58 <+192>:   mov    %eax,%esi				;%eax赋给%esi
   0x08048e5a <+194>:   inc    %edi						;%edi++
   0x08048e5b <+195>:   cmp    $0x5,%edi				;%edi-0x5改变标志位
   0x08048e5e <+198>:   jle    0x8048e52 <phase_6+186>	;`less or equal`小于等于跳
```

循环5次，循环后的栈结构不变，寄存器数值如下

```c
 EAX  0x804b230 (node6) ◂— 0x1b0
 EBX  0x6
 ECX  0xffffcbf0 ◂— 0x1
 EDX  0xffffcbd8 —▸ 0x804b26c (node1) ◂— 0xfd
 EDI  0x6
 ESI  0x804b230 (node6) ◂— 0x1b0
 EBP  0xffffcc08 —▸ 0xffffcc38 ◂— 0x0
 ESP  0xffffcbb0 —▸ 0xffffccf4 —▸ 0xffffcf21 ◂— '/home/P4yl04d/Documents/lab_bomb/bomb'
*EIP  0x8048e5e (phase_6+198) ◂— jle 0x8048e52
```

##### 第八部分*

```asm
   0x08048e60 <+200>:   movl   $0x0,0x8(%esi)			;0放%esi+0x8位置
   0x08048e67 <+207>:   mov    -0x34(%ebp),%esi			;%ebp-0x34位置的值赋给%esi
   0x08048e6a <+210>:   xor    %edi,%edi				;edi置零
   0x08048e6c <+212>:   lea    0x0(%esi,%eiz,1),%esi	;空指令
   0x08048e70 <+216>:   mov    0x8(%esi),%edx			;%esi+0x8位置的值赋给%edx
   0x08048e73 <+219>:   mov    (%esi),%eax				;esi指向值赋给%eax
   0x08048e75 <+221>:   cmp    (%edx),%eax				;%eax-%edx值改变标志位
   0x08048e77 <+223>:   jge    0x8048e7e <phase_6+230>  ;`greater or equal`大于等于跳转
   0x08048e79 <+225>:   call   0x80494fc <explode_bomb>	;不跳就炸了
```

保证`node1`大于等于`node2`，不然就炸了。这里不符合条件，强行改变寄存器`%edx`数值，先往下走着

```zsh
	set $edx=0x804b23c
```

这里得到了要满足的第四个条件：`<node2>`大于等于`node3`。(貌似`<node1>`是传入的立即数`0xfd`，不确定是否可以改变`<node2>`的值，或者改变`%edx`为其他节点上的数)

##### 第九部分*

这里的话，类似前面，相当于一个循环

```asm
   0x08048e7e <+230>:   mov    0x8(%esi),%esi			;%esi+0x8指向地址的值赋给%esi
   0x08048e81 <+233>:   inc    %edi						;%edi++
   0x08048e82 <+234>:   cmp    $0x4,%edi				;%edi-0x4值改变标志位
   0x08048e85 <+237>:   jle    0x8048e70 <phase_6+216>	;`less or equal`小于等于就跳转
```

动态调试下来，发现这次要满足的条件是`<node2>`大于等于`<node3>`。

容易知道：该循环循五次，综合下来需要满足的条件就是

`node1` > = `node2` > = `node3`> = `node4`> = `node5`> = `node6`

猜测输入数字顺序对应节点顺序，于是，根据上面已知的排序

`node4` > `node2` > `node6` > `node3` > `node1` > `node5` 

尝试输入`4 2 6 3 1 5`，运行发现结果正确。

