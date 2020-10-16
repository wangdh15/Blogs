---
title: STL next_permutation算法详解
date: 2020-10-15 20:41:56
categories:
	- C++
	- STL
tags:
	- C++
	- STL
---

## 1. next_permutation 方法

C++中`algorithm`提供了一个方法，用于生成一堆迭代器中间元素的字典序的下一个排列。

比如`abc -> acb -> bac -> bca -> cab -> cba`。

下面是一个函数实例。

```c++
#include <iostream>
#include <algorithm>

using namespace std;

int main() {

    string data = "abcd";
    do {
        cout << data << endl;
    } while (next_permutation(data.begin(), data.end()));  // 在原始数据上操作得到字典序的下一个排列，如果不存在则fanhuifalse
    return 0;
}
```

##2. 算法原理

考虑现在给定一个序列，我们希望得到其下一个序列。

如果从第`i`个位置开始，后面的元素都是非递增排列的，那么对这些元素进行操作，是不可能得到更大的一个排列。

当我们找到了第一个位置`i`，使得`data[i] < data[i + 1]`，且`i + 1` 之后都是非递增排列的，通过对`i`之后的元素进行重排，才能得到下一个更大的排列。

下面考虑如何得到下一个更大的排列。首先，我们需要对第`i`个位置的元素替换为一个比其大的元素，而且是其后面比它大的最小的元素。替换完之后，再将`i`后面的元素按照非递减进行排列，就得到了下一个。

当我们将这两个元素交换之后，可以知道`i`之后的元素是按照非递增排列的，所以只需要将它们逆序即可。

下面是算法的一个实例。

`1 2 3 5 4 `  

找到`3`，然后找到了`4`, 交换，得到`1 2 4 5 3`，然后逆序，得到`1 2 4 3 5`。

下面是算法实现。类似的分析可以得到上一个排列`prev_permutation`的算法。

据cppreference，平均交换次数在3-1.5之间。

```c++
bool next(vector<int>& rec) {
  int n = rec.size();
  if (n == 0 || n == 1) return false;
  int i = n - 2;
  while (i >= 0) {
    if (rec[i] < rec[i + 1]) break;
    else i --;
  }
  if (i == -1) return false;
  int j = n - 1;
  while (rec[j] <= rec[i]) j --;
  swap(rec[i], rec[j]);
  reverse(rec.begin() + i + 1, rec.end());
  return true;
}

bool prev(vector<int>& rec) {
  int n = rec.size();
  if (n == 0 || n == 1) return false;
  int i = n - 2;
  while (i >= 0 && rec[i] <= rec[i + 1]) i --;
  if (i == -1) return false;
  int j = n - 1;
  while (rec[j] >= rec[i]) j --;
  swap(rec[i], rec[j]);
  reverse(rec.begin() + i + 1, rec.end());
  return true;
}
```

## 3.应用

[全排列](https://leetcode-cn.com/problems/permutations/)

[重复元素全排列](https://leetcode-cn.com/problems/permutations-ii/)

