---
title: STL常用容器
date: 2020-04-16 10:04:55
categoties:
	- 数据结构与算法
tags:
	- c++
	- STL
---

### vector

边长数组，倍增思想

系统为某一个程序分配空间时，所需时间和空间大小无关，与申请次数有关。

所以变长数组尽可能减少申请次数。一开始申请一定空间的大小，当超过容量时，申请长度变为二倍的空间，然后将原始空间的元素copy过来。

当初始化的时候不指定空间大小，但是使用的空间有非常大之后需要多次扩容，那么扩容需要的时间复杂度是`O(N)`

相当于插入一个元素就需要将`O(1)`的时间。申请空间的次数是`O(log n)`。

```c++
#include<iostream>
#include<vector>
#include<algorithm>
#include<cstring>
using namespace std;

int main(){
  
  vector<int> a;   // 定义一个vecotr
  vector<int> a(10);  // 初始长度为10；
  vector<int> a(10, 3);  // 初始长度为10， 且每个元素初始化为3
  for(auto i : a) cout << i << " ";  // 遍历元素的方式  ， auto 关键字，系统自动推断变量类型，变量名比较长的时候可以使用
  
  vector<int> a[10];  // 定义了vector数组，每个元素都是一个vector<int>
  
  a.size();  // 返回列表里的元素的个数
  a.empty();  //  返回列表是不是空
  // size() 和empty() 是所有容器都有的，时间复杂度是o(1)
  
  a.clear();  // 清空，不是所有的容器都有
  
  a.front(); // 返回第一个元素
  a.back();  // 返回最后一个元素
  a.push_back(); // 从最后插入一个元素
  a.pop_back(); // 删除最后一个元素
  a.begin();  a.end(); // 迭代器。  可以看做指针，可以解引用
  a[2];   // 支持随机寻址，与数组相同
  
  // 三种遍历方式
  for(int i = 0; i < a.size(); i ++) cout << a[i] << " ";
  cout << endl;
  for(vector<int>::iterator i = a.begin(); i != a.end(); i ++) cout << *i << " ";
  for(auto i = a.begin(); i != a.end(); i ++) cout << *i << " ";  // 迭代器，解引用
  cout << endl;
  for(auto x : a) cout << x << " ";   // auto关键字自动推断类型
  cout << endl;
  
  //  vector支持比较，按位进行字典序比较
  vector<int> a(4, 3), b(3, 4);
  cout << (a < b);
  return 0;
}
```

### pair

元组类型，相当于`Python`中的().

当一个元素有多个属性的时候，可以使用pair来进行存储。当需要按照某个属性来排序的时候，就非常好。

比如合并区间的问题。每个区间有开始和结尾两个属性，然后需要按照区间的开始对区间进行排序。

可以看做实现了一个结构体，且实现了比较器。

```c++
// 两种初始化方法
pair<int, string> p;
p = make_pair(10, "aa");
p = {q, "ab"};

// 支出比较运算，first为第一关键字，second为第二关键字
pair<int, string> p = {1, "aa"};
pair<int, string> q = {1, "ab"};

// first取出第一个字， second取出第二个字
cout << p.first << endl;
cout << p.second << endl;
cout << (p < q) << endl;

// 也可以使用pair存储三个属性等; pair 嵌套pair
pair<int, pair<int, int>> p;  
```

### string

字符串，`substr()`, `c_str()`

```c++
string s = "a";  
s += "ab";  // 支持增量操作
s += 'a';
cout << s << endl;
cout << s.substr(1, 2) << endl;  // 可以取出某个子串，第一个是起始index，第二个是长度。第二个缺省或者超过范围则返回之后的左右字符串
cout << s.substr(1) << endl;
cout << s.substr(1, 100) << endl;
printf("%s\n", s.c_str());  // 使用printf输出的时候，需要找到存储字符串数组的起始地址。c_str()可以返回

size()  | length()   // 返回字符串的长度
  
s.find('a'); // 返回a在s中的位置,如果没找到，返回一个特别的标志c++中用npos表示(可以用s.npos)来表示
```

###queue 

队列，`push()`, `front()` , `pop()`,`back()`

```c++
// push() 向队尾插入一个元素
// front()  返回队头元素
// pop() 返回队尾元素
// back() 弹出队头元素
// size() 
// empty()
// 没有clear操作
```

### priority_queue

优先队列，`push()`, `top()`, `pop()`

```c++
// 内部使用堆维护
// 默认是大顶堆，和python相反
// 使用小顶对方法
// 1. 在插入的时候插入元素的相反数即可
// 2. 如下定义
#include<vector>
#include<queue>
#include<iostream>
#include<csting>
#include<queue>
using namespace std;

priority_queue<int, vector<int>, greater<int>> heap;

// priority_queue<int> q;   // 大顶堆
priority_queue<int, vector<int>, greater<int>> q;  // 小顶堆
q.push(1);
q.push(2);
q.push(5);
q.push(10);
while(!q.empty()){
  cout << q.top() << endl;
  q.pop();
}
```

### stack

栈， `push()`, `top()`, `pop()`

```c++
#include<stack>
// size()
// empty()
// push()  栈顶加入一个元素
// top() 范湖栈顶元素
// pop() 弹出栈顶元素
```

### deque

双端队列，队头队尾都可以插入删除元素，也可以随机访问，加强版vector。

`deque`非常厉害，几乎支持其他容器所有的操作，但是速度对应的也比较慢。

```c++
#include<deque>

size()
empty()
clear()   // 支持清空操作
front()  // 可以访问头部或尾部元素
back()
// 能够从头或者尾部插入或删除元素
push_back()
pop_back()
push_front()
pop_front()
[]  // 支持索引操作
```

### set, map multiset, multimap

基于**平衡二叉树(红黑树)**, 动态维护**有序**序列

```c++
size();
empty();
clear();
begin()/end();  ++ , --  迭代器支持自增自减 返回前驱后继  时间复杂度也是log n

set/ multiset
// multiset可以存储多个相同的元素
insert(); // 插入一个元素 log(n)
find(x); // 查找一个元素
count(x); // 返回某个数的个数
erase(); // 输入一个数x，则删除所有x O(k + log n) k是元素个数  输入一个迭代器，则删除这个迭代器
lower_bound()/ upper_bound();  // lower_bound(x) 返回大于等于x的最小的数的迭代器  upper_bound(x) 返回大于x的最小的数的迭代器


map/ multimap;  // 将两个元素做映射， multi_map支持一个key对应多个value
insert(); // 插入的是一个pair
erase();  // 输入的参数是一个pair或者迭代器
find(); // 
[];  // 支持索引操作 时间复杂度O(log n)

#include<map>

map<string, int> a;
a["wdh"] = 1;
cout << a["wdh"] << endl;
```

### unordered_set, unordered_map, unordered_multiset, unordered_multimap

使用**哈希表**进行存储

操作和上面的相同，时间复杂度都是`O(1)`。内部无续，不支持`lower_bound, upper_bound`。不支迭代器的`++ --` 

### bitset

压位， 进行位运算，如状压DP的对状态的各种位操作。

比如如果开大小为1024的bool数组，那么需要使用1KB内存。

但是如果使用压位，那么只需要使用125B内存。

要开10000 * 10000的bool数组，那么需要大概100MB内存，但是内存限制是64MB， 这个时候使用压位就可以只用

12MB内存。

```c++
bitset<10000> s;  // 一万位
~ & | ^;  // 按位逻辑操作
>> << ;  // 移位操作
==, != ;  // 比较操作
[];   // 取出某一位
count();  // 返回有多少个1
any();  // 判断是否至少有一个1
none();  // 判断是否全为零
set();  // 把所有位置为1
set(k, v); // 将第k位变成v
reset();  // 将所有位变成零
flip(); // 将所有位取反，等价于~
flip(k); // 将第k位取反
```

### algorithm

实现了一些常用算法

```c++
vector<int> a;
sort(a.begin(), a.end());  // 对a进行快速排序
reverse(a.begin(), a.end()); // 翻转一个vector
unique(a.begin(), a.end()); // 返回去重之后的尾迭代器(指针), 前闭后开，返回的是去重之后末尾元素的下一个位置
int m  =unique(a.begin(), a.end()) - a.begin(); // 计算去重之后的元素个数m
random_shuffle(a.begin(), a.end());  // 随机打乱顺序
next_permutation();  // 将两个迭代器(指针)指定的部分看做一个排列，求出这些元素构成的全排列中，字典序排在写一个的全排列。并直接在序列上更新。若不存在，则返回false，否则返回true。同理有prev_permutation函数

// 输出1 - n的 n！中全排列
int q[3] = {1, 2, 3};
do{
  for(int i = 0; i < 3; i ++) cout << q[i] << " ";
  cout << endl;
}while(next_permutation(q, q+3));
```



