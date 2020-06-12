---
title: cpp基础01
date: 2020-06-11 11:14:06
categories:
	- C++
tags:
	- 基础知识
---

###自定义类型

类型别名：为已有类型另外命名

```c++
// typedef 已有类型名  新类型名表    C语言继承来的
typedef pair<int,int> PII, pii
 
// using 新类型名 = 已有类型名   C++ 新特性
  
using Area = double
using Volume = double
```

枚举类型

```c++
// 枚举类型可以解决数据合法性检验

// enum  枚举类型名  {变量值列表}

enum Weekday {SUN, MON, TUE, WED, THU, FRI, SAT};
// 默认情况下 SUN = 0, MON = 1， 之后不能再对SUN进行赋值
// 可以在声明时指定枚举元素的值
enum Weekday {SUN = 7, MON = 1, TUE, ...};  // 后面自动增1， TUE 为2
// 枚举类型可以给整型赋值，但是整型必须强制转化，而且数据在枚举的范围内才可以
// 枚举类型可以关系比较
```

`auto`与`decltype`类型

```c++
// auto ： 编译器通过初始值自动推断变量的类型
vector<int> e(10);
for(auto x:e){} // 自动推断类型

// decltype ：定义一个变量与一表达式的类型相同
decltype(i) j = 2;  // j以2为初始值，类型有i一致
```

### 引用

- 引用是标识符的别名
- 定义一个引用时，必须同时对它进行初始化，指向一个已存在的对象,不能指向其他对象
- 引用大多数作为函数的形参从而实现双向传递。
- 引用不是一个对象，不可以定义一个指针指向它

```c++
/*
函数调用传递值的过程是单向传递，复制，效率低。
传递引用的话是双向传递，效率高
为了避免在调用函数中对参数进行修改，可以加const进行修饰
*/

int i, j;
int &ri = i;

void swap(int &a, int &b){
  int t = a;
  a = b;
  b = t;
}
int i = 1, j = 2;
swap(i , j);
```

### 内联函数

在调用简单函数的时候可以提高运行效率的机制。编译器实现。

编译器可能会优化，我们定义为inline，其并不理会；没有定义inline，但是被优化了。

- 声明的时候使用关键之`inline`
- 编译时再调用处用函数体进行替换，节省了参数传递、控制转移等开销
- 注意
  - 内联函数体内不能有循环和switch语句
  - 内联函数的定义必须出现在内联函数第一次被调用前
  - 对内联函数不能进行异常接口声明

### 默认参数值

- 在带有默认参数值的情况下，有默认值的参数必须在后面。

- 前面对函数原型进行了声明，则在实现的时候不可以写参数的默认值。

```c++
#include <iostream>
using namespace std;

int f(int a, int b = 2);



int main() {
    int a = 1, b = 2;
    int t = f(a);
    cout << t << " " << b << endl;
    return 0;
}

int f(int a, int b = 2){
    return a + b;
}

// 
#include <iostream>
using namespace std;


int f(int a, int b = 1){
    return a + b;
}

int main() {
    int a = 1, b = 2;
    int t = f(a);
    cout << t << " " << b << endl;
    return 0;
}

```

### 函数重载

多态性的实现，而且是**编译期实现**的多态性。允许功能相近的函数在相同的作用域内以相同函数名声明，从而形成重载。方便使用、便于记忆。

- 重载函数的形参必须不同：个数不同或类型不同
- 编译程序将根据实参和形参的类型及个数的最佳匹配来选择调用哪一个函数。
- 编译器不以形参名来区分、编译器**不以返回值来区分**

```c++
double add(double x, double y){
  return x + y
}
int add(int x, int y){
  return x + y;
}
```

如果出现由于默认参数导致的，调用的函数不明确的时候，编译时期就会出错。（如下面的情况）

```c++
#include <iostream>
using namespace std;

int f(int a){
    return a + 1;
}

int f(int a, double b = 1){
    return a - b;
}

int main() {
    int a = 1, b = 2;
    int t = f(a);
    cout << t << " " << b << endl;
    return 0;
}
```

原则就是在编译的时候就能够更具形参确定下来调用的是哪个函数。

### 面向对象程序设计

基本特点：

1. 抽象：对统一类对象的共同属性和行为进行概括、形成类
   - 数据抽象：属性或状态
   - 代码抽象：共有的行为或具有的功能
   - 抽象的实现：类
2. 封装：将抽象出的数据成员、代码成员组合，将他们视为一个整体
   - 目的：增加安全性、简化编程。使用这不用了解具体的细节。只需要通过外部接口，以特定的访问权限，来使用类的成员。
3. 继承：在已有类的基础上，进行扩展形成新的类
4. 多态：同一名称，不同的功能实现方式。
   - 目的：达到行为标识统一，减少程序中标识符的个数。

### 类和对象的设计

```c++
class 类名称{
  
  public:
  	共有成员(外部接口)
      
  private:
  	私有成员
      
  protected:
  	保护型成员

};
```

