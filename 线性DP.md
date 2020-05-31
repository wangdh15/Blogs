---
title: 线性DP
date: 2020-03-27 22:33:25
categories:
	- 数据结构与算法
tags:
	- c++
	- 动态规划
---

## 线性DP

动态规划算法的状态包含多个维度，但在每个维度上具有线性变化的阶段，那么该动态规划算法称作线性DP。

如背包问题，数字三角形，最长公共子序列，最长上升子序列等。

**考虑维数的时候从小到考虑，一维不行考虑二维，二维不行考虑三维**

DP问题的复杂度很好计算为`状态数量*计算每个状态需要的时间`

### 1. 最长公共子序列

#### 算法(O(MN))

利用`a, b`两个字符串的最后一个元素来进行分类。

- 当`a`最后一个元素在公共子序列中,`b`最后一个元素不再的时候，为`f[i, j-1]`
- 当`a`最后一个元素不再，`b`最后一个元素在的时候，为`f[i-1, j]`
- 当`a`, `b`最后一个元素都在的时候，这个时候必须他们最有一个元素相同，为`f[i-1, j-1] + 1`
- 当`a`, `b`最后一个元素都不在的时候，这种情况其实是被前两种情况包含了，所以可以不用进行判断。

#### C++代码

```c++
#include<iostream>

using namespace std;

const int N = 1010;
string a, b;
int n, m;
int dp[N][N];

int main(){
    cin >> n >> m;
    cin >> a >> b;
    for(int i = 1; i<= n; i ++){
        for(int j = 1; j <= m; j ++){
            dp[i][j] = max(dp[i-1][j], dp[i][j-1]);  // 不考虑各自最后一个字符
            if(a[i - 1] == b [j - 1]) dp[i][j] = max(dp[i-1][j-1] + 1, dp[i][j]);  // 考虑各自最后一个字符
        }
    }
    cout << dp[n][m];
    return 0;
}
```

### 2. 最短编辑距离

给定两个字符串，将`a`经过若干操作变为`b`，可进行的操作有

- 删除某一个字符
- 某个位置插入某个字符
- 将某个字符替换为另一个字符

求最少操作。

#### 算法

二维DP问题，考虑`a`中最后一个元素的操作

- 如果是将`a`中最后一个位置删除了，那么需要的操作是`f[i-1, j]  + 1`
- 如果是将`a`中最后一个位置插入了一个元素，那么这个元素一定是`b`中最后一个元素，所以操作为`f[i, j-1] + 1`
- 如果是将`a`中最后一个位置替换为和`b`最后一个位置相同，这个时候需要考虑原本是否就相同，如果相同，则为`f[i-1, j-1]`,否则为`f[i-1, j-1] + 1`。
- 边界情况处理，这个问题和前面的不同，边界不再是零，需要人为初始化一下。

#### c++代码

```c++
#include<iostream>

using namespace std;

const int N = 1010;
int n, m;
string a, b;
int dp[N][N];

int main(){
    
    cin >> n >> a >> m >> b;
    for(int i = 0; i <= n; i ++) dp[i][0] = i;  // 边界初始化
    for(int j = 0; j <= m; j ++) dp[0][j] = j;
    
    for(int i = 1; i <= n; i ++){
        for(int j = 1; j <= m; j ++){
            dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + 1;  // 插入和删除操作
            if(a[i-1] == b[j -1]) dp[i][j]= min(dp[i-1][j-1], dp[i][j]);  // 替换操作
            else dp[i][j] = min(dp[i][j], dp[i-1][j-1]  +1);
        }
    }
    cout << dp[n][m];
    return 0;
}
```

###  3. 最长上升子序列

[题目链接](https://www.acwing.com/problem/content/898/)

##### 解题思路

定义状态`dp[i]`为以第`i`个元素结尾的最长上升子序列的长度。那么就有递推公式

`dp[i] = max(dp[i][k] + 1) for k < i and q[k] < q[i]`

所以朴素的版本如下所示：

时间复杂度$O(N^2)$，空间复杂度$O(N)$

```c++
#include<iostream>

using namespace std;
const int N = 1010;
int q[N];
int n;
int dp[N];

int main(){
    
    cin >> n;
    int res;
    for(int i = 0; i < n; i ++) cin >> q[i];
    for(int i = 0; i < n; i ++){
        dp[i] = 1;
        for(int j = i- 1; j >= 0; j--){
           if(q[i] > q[j]) dp[i] = max(dp[i], 1 + dp[j]); 
        }
        res = max(res, dp[i]);
    }
    cout << res;
    return 0;
}
```

#### 优化版本

每一个元素都需要遍历之前所有的元素，能不能在前面元素遍历之后保存一些信息，能够供以后的元素使用，而不是每次都暴力遍历呢？所以可以使用一些方法来进行优化。通过观察，如果两个最长上升子序列的长度是相同的话，那么我们只需要保存结尾小的那个就行。因为能够拼到结尾大的元素一定也可以拼到结尾小的元素后面。然后我们可以记录每一个长度的最长上升子序列，其结尾最小是多少。同时可以用反证法证明这个最小元素是随着序列长度单调递增的。

这样拿到一个元素之后，我们只需要使用二分查找，找到列表中元素小于当前元素的最大值，其对应的index就是能够拼接的最大长度。所以在每个元素我们寻找其能够拼接的最长的前缀只需要$O(log N)$。整体复杂度变为$O(N log N)$。

#### C++代码

```c++
#include<iostream>

using namespace std;

const int N = 1e6+10;
int q[N];
int dp[N];
int m[N];  // m[i]表示长度为i的上升子序列中，结尾的最小值。
int n;

int f(int i){
  	// 二分查找，找到列表中小于i的最大元素所在的index
    int l = 0;
    int r = n - 1;
    while(l < r){
        int mid = l + r + 1 >> 1;
        if (m[mid] >= i) r = mid - 1;
        else l = mid;
    }
    return l;
}

int main(){
    
    cin >> n;
    for(int i = 0; i < n; i ++) cin >> q[i];
    m[0] = -1e9 -10;
    for(int i = 1; i < n; i ++) m[i] = 1e9 + 10;  // 初始化序列
    int res = 1;
    for(int i = 0; i < n; i ++){
        int index = f(q[i]);
        // cout << index << endl;
        dp[i] = (index  +1);
        if(index + 1 < n && m[index + 1] > q[i]) m[index + 1] = q[i];  // 判断当前元素结尾的序列是否可以更新m
        res = max(res, dp[i]);
    }
    cout << res;
    return 0;
}
```

