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
s  # 源码单步执行
si # 汇编单步执行
c  # 持续运行到下一个断点
```

### 04 显式汇编、源码

```shell
layout split  # 同时显式汇编以及源码
layout sam  # 显式汇编
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

