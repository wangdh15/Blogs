---
title: 数位DP
date: 2020-06-12 22:27:09
categories:
	- 数据结构与算法
tags:
	- 动态规划
---

### 数位DP

技巧：

- 两个边界问题转为一个边界问题
- 转为树的形式

### 度的数量

[原题链接](https://www.acwing.com/problem/content/1083/)

#### 解题思路



#### C++代码

```c++
#include<iostream>
#include<vector>
using namespace std;

const int N = 35;
int X, Y, K, B;
int f[N][N];

int dp(int a){
    
    if(!a) return 0;
    vector<int> nums;
    while(a){
        nums.push_back(a % B);  // 提取B进制每一位
        a /= B;
    }
    
    int ans = 0, last = 0;
    for(int i = nums.size() - 1; i >= 0; i --){  // 从高到低位枚举
        int x = nums[i];
        if (x){  // i为不为零时，才可以放零
            ans += f[i][K - last]; // 剩余位放1
            if(x > 1){  // i位大于1时，才可以放1
                if(K - last - 1 >= 0) ans += f[i][K - last - 1];  // 剩下还有1可以放
                break;  // 大于1，则右分支就不用考虑了
            }else{  
                last ++;  // 当前位等于1,则last自增
                if(last > K) break;  // 右分支1的位数超过了K，则直接break
            }
        }
        if(!i && last == K) ans ++;  // 最右的一个分支可以取的条件是，last 的个数为1
    }
    return ans;
    
}


int main(){

    for(int i = 0; i < N; i ++){  // 预处理好组合数
        for(int j = 0; j <= i; j ++){
            if(!j) f[i][j] = 1;
            else f[i][j] = f[i-1][j-1] + f[i-1][j];
        }
    }
    
    cin >> X >> Y >> K >> B;
    
    cout << dp(Y) - dp(X - 1);   // 转为单区间问题
    return 0;
    
}
```

### 数字游戏

[原题链接](https://www.acwing.com/problem/content/1084/)

#### 解题思路

#### C++代码

```c++
#include<iostream>
#include<vector>
using namespace std;

const int N = 15;
int f[N][N];


int dp(int a){
    
    if(!a) return 1;
    vector<int> nums;
    
    while(a){
        nums.push_back(a % 10);
        a /= 10;
    }
    int ans = 0;
    int last = 0;
    for(int i = nums.size() - 1; i >= 0; i --){
        int x = nums[i];
        for(int j = last; j < x; j ++){
            ans += f[i + 1][j];
        }
        if( x < last) break;
        last = x;
        if(!i) ans ++;
    }
    return ans;
    
}

int main(){
    
    for(int i = 0; i < 10; i ++)f[1][i]  = 1;  // i位，最高位为j的个数
    for(int i = 2; i < N; i ++){  // 预处理左侧分支的情况
        for(int j = 0; j < 10; j ++){
            for(int k = j; k < 10; k ++){
                f[i][j] += f[i-1][k];
            }
        }
    }
    
    int x, y;
    
    while( cin >> x >> y) cout << dp(y) - dp(x - 1) << endl;
    return 0;
    
}
```

### Windy数

[原题链接](https://www.acwing.com/problem/content/1085/)

#### 解题思路

#### C++代码

```c++
#include<iostream>
#include<vector>
using namespace std;

const int N  = 12;
int f[N][N];


int dp(int a){
    
    if(!a) return 0;
    vector<int> nums;
    while(a){
        nums.push_back(a % 10);
        a /= 10;
    }
    int ans = 0;
    int last = -2;
    for(int i = 1; i < nums.size(); i ++){  // 前导零的情况统一处理
        for(int j = 1; j <= 9; j ++){
            ans += f[i][j];
        }
    }
    
    for(int i = nums.size() - 1; i >= 0; i --){
        int x = nums[i];
        int j = i == nums.size() - 1 ;  // 第一个数从1开始，后面从0开始
        for(; j < x; j ++){
            if(abs(j - last) >= 2) ans += f[i + 1][j];  
        }
        if(abs(last - x) < 2) break;
        else last  = x;
        if(!i) ans ++;  // 最后一个右分支，也就是输入的数是否合法
    }
    return ans;
}

int main(){
    
    for(int i = 0; i < 10; i ++) f[1][i] = 1;  // 左侧分支预处理
    for(int i = 2; i < N; i ++){
        for(int j = 0; j < 10; j ++){
            for(int k = 0; k < 10; k ++){
                if(abs(j - k) >= 2) f[i][j] += f[i-1][k];
            }
        } 
    }
    
    int A, B;
    
    cin >> A >> B;
    cout << dp(B) - dp(A - 1);
    return 0;
    
    
}
```

### 数字游戏II

[原题链接](https://www.acwing.com/problem/content/1086/)

#### 解题思路

#### C++代码

```c++
#include<iostream>
#include<vector>
#include<cstring>
using namespace std;

const int N = 12, M = 110;
int f[N][10][M];
int a, b, n;

int dp(int a){
    
    if(!a) return 1;
    vector<int> nums;
    while(a){
        nums.push_back(a % 10);
        a /= 10;
    }
    int ans = 0;
    int last = 0;
    for(int i = nums.size() - 1; i >= 0; i --){
        int x = nums[i];
        for(int j = 0;  j < x; j ++){
            ans += f[i+1][j][((-last) % n + n) % n];
        }
        last += x;
        if(!i && last % n == 0) ans ++; 
    }
    return ans;
    
}

int main(){
    
    while(cin >> a >> b >> n){
        memset(f, 0, sizeof f);
        for(int i = 0; i <= 9; i ++) f[1][i][i % n] ++;
        
        for(int i = 2; i < N; i ++){
            for(int j = 0; j <= 9; j ++){
                for(int k = 0; k < n; k ++){
                    for(int l = 0; l <= 9; l ++){
                        f[i][j][k] += f[i-1][l][((k - j) % n + n) % n];
                    }
                }
            }
        }
        
        cout << dp(b) - dp(a - 1) << endl;
    }
    
    return 0;
}
```

