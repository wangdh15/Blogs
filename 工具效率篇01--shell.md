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
# ’ ‘之间的字符串是字面的意思，不会像" "中的字符串被替换变量,” “中的一些命令也会被执行，然后将替换替换到对应的位置

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
$_  # 上一个命令的最后一个参数，交互界面也可以通过ESC + . 获取, 通常用于上下两条命令参数相同的情况

# 将一些常用的命令以函数的形式写到文件中，然后source就可以将文件中的内容加载到环境中，之后就可以直接调用文件中的函数
source file 
```

命令通常会通过`STDOUT`返回输出，通过`STDERR`返回error。返回值为`0`表示一切正常，其他返回值都表示某些error发生。

命令的返回值可以用来作为条件语句。

```shell
# 在逻辑运算的时候是一样的逻辑
# 下面是一些逻辑运算短路的特性
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

#### Shell脚本实例

```shell
# 1 
#!/usr/bin/env bash

echo "Starting program at $(date)" # Date will be substituted

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # When pattern is not found, grep has exit status 1
    # We redirect STDOUT and STDERR to a null register since we do not care about them
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done

# 2
#!/usr/bin/env bash
a=0
res=-1
let a+=1
$(./aaa.sh >> ok.txt 2> err.txt)
while [[ $? -eq 0 ]]; dos
    let a+=1
    $(./aaa.sh >> ok.txt 2> err.txt)
done
echo $as
```

```shell
# 将某个命令的结果作为参数，用$()将这个命令包围起来即可
echo $(pwd)
echo "We are in $(pwd)"

# 通配符
# * 表示任意多个字符
# ? 表示任意一个字符
# 可以使用下面的方式来进行扩展
mv aa.txt aa.sh
mv aa.{txt,sh}  # 自动扩展为上面的命令
touch foo{,1,2,3}  # 创建foo foo1 foo2 foo3文件
touch project{1,2}/src/test/test{1,2,3}.py  # 分别在两个目录下创建三个文件
touch {foo,bar}/{a..j}  # 在foo bar文件夹下创建a 到 j 文件

# diff查看两个文件的差异
diff <(ls foo) <(ls bar)  # 显示foo bar文件夹的内容，并比较不同
mkidr {foo,bar}
touch foo/x bar/y
diff <(ls foo) <(ls bar)

# 配置python脚本的运行
# 不同机器pyhton的位置不同，可以直接在python脚本第一行加入下面的命令
#!/usr/bin/env python   # 在不同机器上都可以工作

# 对shell脚本进行检查
shellcheck file # shellcheck是一个检查工具，对可能的错误给出提示

# man可以查看命令的详细介绍，但是有时候过于冗长，
# tldr 可以直接给出一些命令常用的搭配 (强烈推荐)
tldr tar
```

#### 查找文件/代码

```shell
# find 命令
find . -name src -type d  # 递归寻找当前目录下，名字为src的文件夹
find . -path '**/test/*.py' -type f   # 寻找当前目录下，test文件夹下的py文件
find . -mtine -1  # 倒数第一天修改的文件
find . -name "*.tmp" -exec rm {} \ # 查找到文件，并执行删除命令，可以执行其他命令 (tldr)查看

# 其他查找命令
fd 
# locate命令，文件系统建立了索引，所以可以较高效地查找
locate tmp  # 寻找文件系统中包含tmp的路径

# grep
grep foobar mcd.sh  # 查找mcd中是否存在foobar字符串
grep -R foobar .  # 查找当前文件夹下所有文件是否存在foobar字符串

# 查找不符合模式的文件
rg -u --files-without-match "^#\!" -t sh  # 查找shell脚本中没有#!的文件
```

#### 查找历史命令

```shell
# 使用history可以查看历史输入的命令
history | grep ls # 查看历史命令中包含ls的
# Ctrl + R 然后输入查询
# fzf工具
```

#### 其他工具

```shell
# tree 列出目录的树形结构
# broot 列出目录结构，可以进行跳转 搜索等，可以进行交互
#  nnn  也可以列出目录结构，进行文件夹的跳转等， 使用上下左右箭头
# auto jump  直接跳转到某个路径，可以根据历史智能匹配，输入几个字母即可
```

### Shell各种重定向

- 0:标准输入(stdin)
- 1:标准输出(stdout)
- 2:标准错误(stderr)

```shell
ls a b 1>f.out 2>f.err # 终端不输出，相应的输出会到文件f.out 和 f.err中
```

`1>f.out`缩写成`>f.out`,通常将1省略

```shell
ls a b 1>f.out 2>&1  # 将所有标准输出，标准错误输出到f.out
ls a b 2>f.out 1>&2  # 一样的功能
# 写反是错误的
ls a b 2>&1 1>f.out  # 标准错误输出到终端，标准输出输出到f.out
# 1>&2 中的&可以理解为一个转义符。 直接1>2的2表示一个文件，1>&2 中2表示标准错误
```

总结：

- `&`是一个描述符，`1`和`2`前面不加的话，会当做一个普通文件
- `>`是`1>`的缩写
- `1>&2`标准输出重定向到标准错误
- `2>&1`标准错误重定向到标准输出
- `&> file`和`>& file`将标准输出和标准错误都重定向到问价`file`中

