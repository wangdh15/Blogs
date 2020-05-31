---
title: 树型DP
date: 2020-04-15 14:02:37
categories:
	- 数据结构与算法 
tags:
	- 动态规划
---

树型DP主要是在树上做DP，一般的子问题是定义在子树上的问题。某课树的问题的解可以由其子问题的解得到。

一般是使用`dfs`进行处理。

### 1. 树的重心

[原题链接](https://www.acwing.com/problem/content/848/)

#### 解题思路

可以求解出每个点删除之后的结果，然后去最大的最小。

无向数可以定一个遍历顺序。当删除一个节点之后，这个节点的孩子的节点数可以得到。那么剩下的连通分量的节点个数

可以直接使用总节点个数减去其孩子节点个数和减去1。所以在解决这个问题的时候，每个子问题返回以自己为根

的子树的节点数即可，同时动态维护结果。

时间复杂度`O(N)`

#### c++代码

```c++
#include<iostream>
#include<vector>

using namespace std;

const int N = 100010;

vector<int> e[N];
int n;
bool vis[N];
int res = 100010;


int dfs(int a){
    
    vis[a] = true;
    int sum = 0;
    int _m = 0;
    for(auto x : e[a]){
        if(vis[x]) continue;
        else{
            int aaa = dfs(x);  // 对每个子节点进行深度优先搜索
            sum += aaa;
            _m = max(aaa, _m);  // 删除当前节点的连通分量最大值
        }
    }
    _m = max(_m ,n-1-sum);  
    res = min(res, _m);  // 维护res
    return sum + 1;   // 返回子树规模
}

int main(){
    
    cin >> n;
    for(int i=0;i<n-1;i++){
        int a, b;
        cin >>a>>b;
        e[a].push_back(b);
        e[b].push_back(a);
    }
    
    dfs(1);
    cout << res;
    
    return 0;
}
```

### 没有上司的舞会

[题目链接]()

