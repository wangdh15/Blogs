---
title: 系统级IO
date: 2020-07-31 23:19:57
categories:
	- CSAPP
tags:
	- 基础知识
	- CSAPP
mathjax: true
---

### 1. Unix IO

Linux文件就是一个字节序列。

$$B_0, B_1, ..., B_n$$

所有的**IO设备**(网络、终端、磁盘)都**被模型化为文件**，所有的输入输出都被当做对相应文件的读和写来执行。

将设备映射为文件，Linux内核引出一个简单、低级的应用接口，使得所有的输入和输出可以以一致统一的方式来执行：

1. 打开文件，调用操作系统提供的服务，内核返回一个非负整数，称为**文件描述符**。后续操作传入这个标识符即可。内核记录有关这个打开文件的所有信息，应用程序只需要记住这个描述符。

   Linux shell创建的每个进程开始时都有三个打开的文件。

   - 标准输入：0
   - 标准输出：1
   - 标准错误：2

2. 修改当前文件位置，对每个打开的文件，内核保持一个文件位置`k`，表示从文件开头起始的字节偏移量，初始为0。可以通过`seek`操作显式指定`k`。
3. 读写文件。读操作是从文件复制`n`个字节到内存(一般配合缓冲区)，然后将`k`增加到`k + n`。长度为`m`字节的文件，当`k >= m`时，指定读操作会触发`end-of-file（EOF）`条件。文件结尾并没有明确的"EOF"符号。
4. 关闭文件。通知内核关闭文件。内核释放文件打开时创建的数据结构，将描述符恢复到可以的描述符池中。无论一个进程因为哪种原因终止，内核都会关闭所有打开的文件并释放他们的内存资源。

### 2. 内核结构

内核用三个相关的数据结构来表示打开的文件。

1. 描述符表，每个进程都有自己的描述符表，表项由进程的打开的文件描述符来索引，每个表项指向一个文件表中的一个表项。

2. 文件表。打开文件的集合由一张文件表来表示，所有进程共享这个文件表。每个表项由如下组成:

   - 当前的文件位置
   - 引用计数
   - 指向`v-node`表中对应表项的指针

   关闭一个描述符会减少对应的文件表项中的引用计数。当引用计数为零时，内核删除这个文件表项。

<img src="系统级IO/01.png" alt="image-20200801005812999" style="zoom: 67%;" />

多个描述符也可以通过不同的文件表项来引用同一个文件。比如对同一个`filename`调用两次`open`函数。

<img src="系统级IO/02.png" alt="image-20200801010035134" style="zoom:67%;" />

父子进程共享文件如下图所示.

<img src="系统级IO/03.png" alt="image-20200801010129982" style="zoom:67%;" />

子进程有一个父进程的描述符表的副本。在内核删除相应文件表之前，父子进程必须都关闭描述符。

而且一个进程对某个文件描述符的操作，也会反映到另一个进程中。

### 3. IO重定向

重定向就是将文件描述符修改为其他对应的内容。

<img src="系统级IO/04.png" alt="image-20200801010516948" style="zoom:67%;" />

使用`dup2`系统调用，可以将复制描述符表项。

```c
#include <unistd.h>

int dup2(int oldfd, int newfd); // 覆盖oldfd的内容到newfd中
```

上图中，为调用`dup(4, 1)`的结果。这样任何写到标准输出的内容都被重定向到了文件B中。

### 4. 标准IO

C语言定义了一组高级输入输出函数，为标准I/O库。提供了如下的函数

1. 打开关闭文件`fopen/fclose`
2. 读写字节`fread/fwrite`
3. 读写字符串`fgets/fputs`
4. 格式化IO`scanf/printf`

标准IO库将打开的文件模型化为一个**流**。一个流就是一个指向`FILE`类型的指针。类型为`FILE`的流是对**文件描述符**和**流缓冲区**的抽象。使用缓冲区的目的是使开销较高的Linux I/O系统调用的数量尽可能得小。先对缓冲区操作，然后再缓冲区满之后再调用系统调用进程操作。

每个C程序开始时都有三个打开的流，`stdin`、`stdout`、`stderr`，分别对应标准输入，标准输出和标准错误。

```c
#include <stdio.h>
extern FILE *stdin;  / 标注输入，代表描述符0
extern FILE *stdout; / 标注输出，代表描述符1
extern FILE *stderr; / 标注错误，代表描述符2
```

```c
#include <stdio.h>

int main() {
    printf("Stdout Hello World\n");
    fprintf(stdout, "Stdout Hello World\n");
    perror("Stderr Hello World\n");
    fprintf(stderr, "Stderr Hello World\n");
    return 0;
}
```

- `stderr`没有缓冲，立即输出。
- `stdout`默认是缓冲，遇到`\n`向外输出内容
- 如果想实时输出，则加上`fflush(stdout)`，从而达到实时输出的效果

可以对下面的代码进行断电调试，从而感受这种现象。

```c
#include <cstdio>

int main() {
    printf("Stdout Hello World");
    fflush(stdout);
    fprintf(stdout, "Stdout Hello World\n");
    perror("Stderr Hello World");
    fprintf(stderr, "Stderr Hello World\n");
    return 0;
}
```

<img src="系统级IO/05.png" alt="image-20200801010858509" style="zoom:67%;" />







