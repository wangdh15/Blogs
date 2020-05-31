---
title: 工具效率篇
date: 2020-05-27 00:27:09
categories:
	- 常用工具 
tags:
	- shell
---

内容来自[MIT-6-NULL](https://missing.csail.mit.edu/2020/course-shell/)

### 01. Shell基础

```shell
echo "Message"  # 输出Message， 当Message中不存在空格时，可以去除双引号，或者使用\转义
echo Hello\ World  ==  echo "Hello Word"
echo $PATH  # 输出环境变量PATH

which echo   # which输出程序所在的文件夹
.   # 当前路径
..  # 上一层路径
pwd  # 输出当前所在位置的绝对路径
~  # 当前用户的家目录
ls  # 列出当前路径的所有内容


# 权限
w  # allowed to modify the directory 
x  # allowed to enter the directory, have "search" permissions on that directory, for file ,execute
r  # allowed to list the contents of directory
chmod 755 foo  # 将foo权限修改为755

# 文件操作
cp src dis  # copy文件从src到dis
mv src dis  # 移动文件，常用了对文件重命名
mkdir foo   # 创建文件夹
man ls      # 有不会的，找男人(man)

# 程序之间的连接
# 重定向
> file  # 将程序的stdout到文件
< file  # 将文件中的内容作为程序的stdin
echo hello > hello.txt
cat < hello.txt
cat < hello.txt > hello2.txt
>> file # 对文件进行追加写
|   # 管道，将一个程序的输出和另一个的输入拼接起来
ls -l | tail -n1
curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2

tee # 读取标准输入的数据，将其内容输出成文件
ls -l | tee log.txt
```

在键入命令的时候，`shell`会从`PATH`环境变量中的路径进行查找对应的可执行程序。如果安装了一些软件、可能需要对`PATH`进行添加对应的路径。

### 02.Shell编程

Shell脚本编程针对shell相关的任务进行了优化。所以创建命令pipelines，将结果存储成文件，从标注输入读取在shell中比通常的及脚本语言更加方便。

```shell
# 变量分配
foo=bar
# foo = bar 不可行。其会被解释为调用foo程序，参数为= 和 bar。在shell脚本中，空格承担参数分割的功能，所以不可随意使用
$foo  # 使用foo变量
echo "$foo"  # print bar
echo '$foo'  # print $foo
# ’ ‘之间的字符串是字面的意思，不会像" "中的字符串被替换变量

# shell支持if, case, while, for.下面是定义的一个函数
mcd () {
	mkdir -p "$1"
	cd "$1"
}

# bash的一些特殊变量
$0   # 脚本名称
$1 - $9  # 脚本的参数，$1 是第一个
$@  # 所欲参数
$#  # 参数个数
$?  # 上一个命令的返回值
$$  # 目前脚本的PID
!!  # 整个上一个指令。通常使用情况是，执行上一个命令由于权限问题失败，可以直接执行sudo !!来重新执行
$_  # 上一个命令的最后一个参数，交互界面也可以通过ESC + . 获取
```

命令通常会通过`STDOUT`返回输出，通过`STDERR`返回error。返回值为`0`表示一切正常，其他返回值都表示某些error发生。

命令的返回值可以用来作为条件语句。

```shell
false || echo "Oops, fail"
# Oops, fail

true || echo "Will not be printed"
#

true && echo "Things went well"
# Things went well

false && echo "Will not be printed"
#

false ; echo "This will always run"
# This will always run
```