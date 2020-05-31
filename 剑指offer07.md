---
title: 剑指offer07
date: 2020-05-07 13:48:49
tags:
---

### 67. 滑动窗口的最大值

[原题链接](https://www.acwing.com/problem/content/75/)

#### 解题思路

使用单调队列，保持队列的单调递减性。 如果要求最小值的话，需要维护队列的单调递增性。

需要从两端删除，所以使用双端队列。

#### C++代码

```c++
#include<deque>
typedef pair<int,int> PII;
class Solution {
public:
    vector<int> maxInWindows(vector<int>& nums, int k) {
        deque<PII> dq;
        vector<int> ans;
        for(int i = 0; i < nums.size(); i ++){
            while(dq.size() && dq.back().first <= nums[i]) dq.pop_back();
            dq.push_back({nums[i], i});
            if(dq.front().second + k <= i) dq.pop_front();
            if(i >= k - 1) ans.push_back(dq.front().first);
        }
        return ans;
    }
};
```

#### Python代码

```python
class Solution(object):
    def maxInWindows(self, nums, k):
        """
        :type nums: List[int]
        :type k: int
        :rtype: List[int]
        """
        t = []
        res = []
        for i in range(len(nums)):
            while t and t[-1][0] <= nums[i]:
                t.pop()
            t.append([nums[i], i])
            if t[0][1] + k <= i:
                t.pop(0)
            if i >= k - 1:
                res.append(t[0][0])
        return res
```

### 68. 骰子的点数

[原题链接](https://www.acwing.com/problem/content/76/)

#### 解题思路

相当于一个分组背包问题。每组物品中都是`1-6`。然后求方案数。

动态规划，定义`dp[i][j]`为`i`次掷出`j`的方法。那么按照最后一次掷的点数进行分类。

$$dp[i][j] = \sum_{k = 1}^{6} dp[i-1][j - k] (j - k >= 1)$$

时间复杂度:每个`i`要计算`5i`个状态，每个状态需要计算6次。所以时间复杂度为$O(n^2)$

空间复杂度，可以使用滚动数组，复杂度为`O(n)`

#### C++代码

```c++
class Solution {
public:
    vector<int> numberOfDice(int n) {
        vector<vector<int>> dp = vector<vector<int>>(n + 1, vector<int>(6 * n + 1, 0));
        vector<int> ans;
        for(int i = 1;  i<= 6; i ++) dp[1][i] = 1;
        for(int i = 2; i <= n; i ++){
            for(int j = i; j <= i * 6; j ++){
                for(int k = 1; k <= 6; k ++){
                    if(j - k >= 1) dp[i][j] += dp[i-1][j-k];
                }
            }
        }
        for(int i = n;  i<= 6 * n; i ++){
            ans.push_back(dp[n][i]);
        }
        return ans;
    }
};
```

#### Python代码

```python
class Solution(object):
    def numberOfDice(self, n):
        """
        :type n: int
        :rtype: List[int]
        """
        dp = [0] * (6 * n + 1)
        for i in range(1, 7):
            dp[i] = 1
        
        for i in range(2, n + 1):
            for j in range(i - 1):  # 需要将前面的数清空，不然会对后面计算造成影响
                dp[j] = 0
            for j in range(6 * i, i - 1 , -1):   # 需要逆序扫描
                dp[j] = 0;
                for k in range(1, 7):
                    if j - k >= 1:
                        dp[j] += dp[j - k]
        return dp[n:6*n+1]
```

### 69. 扑克牌的顺子

[原题链接](https://www.acwing.com/problem/content/77/)

#### 解题思路

先将所有数据排序。统计其中0的个数，作为可以填充空位的个数。

然后扫描数据，如果有两个相同，则一定不行。如果相邻两个不为0的数之差不为1，那么就统计需要几个数来填空。

最终判断空的个数和0的个数即可。

#### C++代码

```c++
class Solution {
public:
    bool isContinuous( vector<int> nums ) {
        if(nums.size() != 5) return false;
        sort(nums.begin(), nums.end());
        int n0 = 0;
        int tt = 0;
        for(auto x:nums){
            if(x == 0) n0 ++;
        }
        for(int i = 1; i < nums.size(); i ++){
            if(nums[i] != 0 && nums[i-1] != 0){
                if(nums[i] == nums[i - 1]) return false;
                else if(nums[i] - nums[i - 1] != 1){
                    tt += nums[i] - nums[i - 1] - 1;
                }
            }
        }
        if(tt <= n0) return true;
        else return false;
        
    }
};
```

#### Python代码

```python
class Solution(object):
    def isContinuous(self, nums):
        """
        :type numbers: List[int]
        :rtype: bool
        """
        if len(nums) != 5:
            return False
        nums.sort()
        n0 = 0
        nt = 0
        for x in nums:
            if x == 0:
                n0 += 1
            else:
                break
        for i in range(1, len(nums)):
            if nums[i] != 0 and nums[i-1] != 0:
                if nums[i] == nums[i-1]:
                    return False
                elif nums[i] - nums[i - 1] != 1:
                    nt += nums[i] - nums[i-1] - 1
                else:
                    pass
        return nt <= n0
```

### 70. 圆圈中最后剩下的数字

[原题链接](https://www.acwing.com/problem/content/78/)

#### 解题思路

约瑟夫问题，直接使用数学解即可。

假设`n-1`个人最后留下来的是`k`,那么`n`个人中，第一个先走的人的编号是`(m - 1) % n`。

这样就相当于 `(m - 1) % n + 1` 和`n-1`时候的零对应，那么留下的人在`n`个人中的编号就是

$$f(n) = ((m - 1) \% n + 1 + f(n - 1)) \% n$$

直接递归求解即可。

边界情况$$f(1) = 0, f(2) = m \& 1$$

#### C++代码

```c++
class Solution {
public:
    int lastRemaining(int n, int m){
        if(n == 1) return 0;
        if(n == 2) return m & 1;
        return ((m - 1) % n + 1 + lastRemaining(n - 1, m)) % n;
    }
};
```

#### Python代码

```python
class Solution(object):
    def lastRemaining(self, n, m):
        """
        :type n: int
        :type m: int
        :rtype: int
        """
        if n == 1:
            return 0
        if n == 2:
            return m & 1
        dp = [0] * (n + 1)
        dp[1] = 0
        dp[2] = m & 1
        for i in range(2, n + 1):
            dp[i] = ((m - 1) % i + 1 + dp[i-1]) % i
        return dp[n]
```

### 71. 股票的最大利润

[原题链接](https://www.acwing.com/problem/content/79/)

####  解题思路

维护一个当前天之前的股票的最低价格，并用当天的价格减去历史最低价格更新最大利润。同时维护历史最低价格。

#### C++代码

```c++
class Solution {
public:
    int maxDiff(vector<int>& nums) {
        if(nums.empty()) return 0;
        int res = 0;
        int mi = nums[0];
        for(int i = 1; i < nums.size(); i ++){
            res = max(res, nums[i] - mi);
            mi = min(mi, nums[i]);
        }
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def maxDiff(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        res = 0
        mi = nums[0]
        for x in nums[1:]:
            res = max(res, x - mi)
            mi = min(mi, x)
        return res
```

### 72. 求1+2+…+n

[原题链接](https://www.acwing.com/problem/content/80/)

#### 解题思路

。。。语法技巧

#### C++代码

```c++
class Solution {
public:
    int getSum(int n) {
        int res = n;
        n > 0 && (res += getSum(n - 1));
        return res;
    }
};
```

### 73. 不用加减乘除做加法

[原题链接](https://www.acwing.com/problem/content/81/)

#### 解题思路

人工模拟二进制加法。

#### C++代码

```c++
class Solution {
public:
    int add(int num1, int num2){
        int res = 0;
        int c = 0;
        for(int i = 0; i < 32; i ++){
            int t1 = (1 << i) & num1, t2 = (1 << i) & num2;
            int rr = t1 ^ t2 ^ (c << i);
            res = res | rr;
            if(t1 && c || t2 && c || t1 && t2) c = 1;
            else c = 0;
        }
        return res;
    }
};

class Solution {
public:
    int add(int num1, int num2){
        while(num2){  // 递归地求解
            int s = num1 ^ num2;  // 将不进位的和记录下来
            int c = (num1 & num2) << 1;  // 将所有的进位信息记录下来
            num1 = s;
            num2 = c;
        }
        return num1;
    }
};
```

### 74. 构建乘积数组

[原题链接](https://www.acwing.com/problem/content/82/)

#### 解题思路

两次遍历，第一次遍历计算前缀乘积，第二次遍历计算后缀乘积。

#### C++代码

```c++
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        vector<int> res = vector<int>(A.size());
        int t = 1;
        for(int i = 0; i < A.size(); i ++){
            res[i] = t;
            t *= A[i];
        }
        t = 1;
        for(int i = A.size() -1; i >= 0; i --){
            res[i] *= t;
            t *= A[i];
        }
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def multiply(self, A):
        """
        :type A: List[int]
        :rtype: List[int]
        """
        res = [0] * len(A)
        t = 1
        for i in range(len(A)):
            res[i] = t
            t *= A[i]
        t = 1
        for i in range(len(A) - 1, -1, -1):
            res[i] *= t
            t *= A[i]
        return res
```

### 75. 把字符串转换成整数

[原题链接](https://www.acwing.com/problem/content/83/)

#### 解题思路

直接进行模拟。

#### C++代码

```c++
class Solution {
public:
    int strToInt(string str) {
        bool fb = false;
        bool mis = false;
        long long  res = 0;
        for(int i = 0; i < str.size(); i ++){
            if(!fb){
                if(str[i] == ' ')continue;
                else if(str[i] == '+' || str[i] == '-' || str[i] >= '0' && str[i] <= '9'){
                    fb = true;
                    if(str[i] == '-') mis = true;
                    if(str[i] >= '0'  && str[i] <= '9') res = res * 10 + str[i] - '0';
                }else{
                    return 0;
                }
            }else{
                if(str[i] >= '0' && str[i] <= '9'){
                    res = res * 10 + str[i] - '0';
                    if(!mis && res > ((long long)1 << 31 - 1)) return ((long long )1 << 31) - 1;
                    if(mis && res > ((long long )1 << 31)) return (-1) * ((long long )1 << 31);
                }else{
                    break;
                }
            }
        }
        if(mis) return -1 * res;
        else return res;
    }
};
```

### 76. 树中两个结点的最低公共祖先

[原题链接](https://www.acwing.com/problem/content/84/)

#### 解题思路

1. 分别找到根节点到两个节点的路径，返回路径公共节点的最后一个。
2. 递归的方法。如果两个节点分别在两个子树里，那么当前节点是最近公共祖先。
3. 如果递归的时候遇到了一个节点，那么就直接返回这个节点。因为：
   - 如果另一个节点在其下，则刚好这个节点就是答案
   - 如果另一个节点不再其下，则上面的某个节点一定会左右都返回了值，不影响最终的答案。

#### C++代码

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root) return NULL;
        if(root == p || root == q) return root;
        TreeNode* left = lowestCommonAncestor(root -> left, p ,q);
        TreeNode* right = lowestCommonAncestor(root -> right, p, q);
        if(left && right) return root;
        if(left) return left;
        else return right;
    }
};
```

#### Python代码

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):
    def lowestCommonAncestor(self, root, p, q):
        """
        :type root: TreeNode
        :type p: TreeNode
        :type q: TreeNode
        :rtype: TreeNode
        """
        if not root:
            return None
        if root == p or root == q:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        elif left:
            return left
        else:
            return right
```

