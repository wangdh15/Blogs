---
title: GDB常用命令
date: 2020-12-21 18:06:06
categories:
	- 常用工具
tags:
	- 基础知识
	- C++
	- 基础工具

mathjax: true
---

## GDB常用命令(未完持续补充)

如果希望用GDB进行调试的话，需要在编译的时候加入`-g`，使得保留符号表。

### 01 启动与退出

```shell
gdb a.out  # 加载a.out可执行程序
run <p1> <p2> ... # 执行，相当于a.out p1 p2
quit   # 退出
```

### 02 打断点

```shell
b <fun_name> # 在函数fun_name的地方加断点
b *0x7c00  # 在虚拟内存0x7c00的地方打断点，运行到这个地址的时候就停止

```

### 03 执行

```shell
s  # 源码单步执行，遇到函数的话进入
si # 汇编单步执行，遇到函数进入
n  # 源码单步执行，遇到函数不跳入
ni # 汇编单步执行，遇到函数不跳入
c  # 持续运行到下一个断点
```

### 04 显式汇编、源码

```shell
layout split  # 同时显式汇编以及源码
layout asm  # 显式汇编
layout src  # 显式源码

Ctrl + L  # 刷新屏幕，有些时候会发生错位
```

### 05 查看信息

```shell
p <var_name>  # 查看变量var_name的值
p $eax  # 查看eax寄存器的值
p *0x7c00  # 查看地址0x7c00地方的值
watch  
info registers  # 查看当前所有寄存器的值
info + others  # 将others替换为其它关键字可以查看很多其它的信息，比如当前打的断点信息等等
							 # 可以查阅手册或者直接在gdb中输入info查看提示信息
info stack / bt / backtrace  # 查看调用栈信息
```

###06 查看内存值

```shell
### gdb help 信息
(gdb) help x
Examine memory: x/FMT ADDRESS.
ADDRESS is an expression for the memory address to examine.
FMT is a repeat count followed by a format letter and a size letter.
Format letters are o(octal), x(hex), d(decimal), u(unsigned decimal),
  t(binary), f(float), a(address), i(instruction), c(char) and s(string).
Size letters are b(byte), h(halfword), w(word), g(giant, 8 bytes).
The specified number of objects of the specified size are printed
according to the format.
Defaults for format and size letters are those previously used.
Default count is 1.  Default address is following last thing printed
with this command or "print".
(gdb) x /6cb 0x804835c //打印地址0x804835c起始的内存内容，连续6个字节，以字符格式输出。
### 
# example
x /6cb 0x804835c //打印地址0x804835c起始的内存内容，连续6个字节，以字符格式输出。
x /sb 0x402400   // 打印地址0x402400起始的字符串，遇到\0为止
x /52db 0x402400  // 打印0x402400开始的52字节的内容，每个自己的内容按照10进制展示


#### print
print [[OPTION]... --] [/FMT] [EXP]  // 其中FMT没有x中的count和size letters
# example
print /s (cha*)$rdi   // %rdi寄存器中存放着一个内存地址，将这个内存地址转为(char*)，然后打印这个地方开始的字符串。和 x /s $rdi 一样的效果
```

