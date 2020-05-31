---
title: 剑指offer06
date: 2020-05-07 02:21:04
categories:
	- 数据结构与算法
tags:
	- 剑指offer
mathjax: true
---

### 56. 0到n-1中缺失的数字

[原题链接](https://www.acwing.com/problem/content/64/)

#### 解题思路

二分法，找到第一个下标不等于自己的位置。然后返回。

#### C++代码

```c++
class Solution {
public:
    int getMissingNumber(vector<int>& nums) {
        if(nums.empty()) return 0;
        int l = 0, r = nums.size() - 1;
        while(l < r){
            int mid = l + r >> 1;
            if(nums[mid] != mid) r = mid;
            else l = mid + 1;
        }
        if(nums[l] == l) l ++;  // 边界情况，所有位置下标等于自己，返回l + 1
        return l;
    }
};
```

#### Python代码

```python
class Solution(object):
    def getMissingNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if not nums:
            return 0
        l = 0
        r = len(nums) - 1
        while l < r:
            mid = l + r >> 1
            if nums[mid] != mid:
                r = mid
            else:
                l = mid + 1
        if nums[r] == r:
            r += 1
        return r
```

### 57. 数组中数值和下标相等的元素

[原题链接](https://www.acwing.com/problem/content/65/)

#### 解题思路

二分法，因为有序，而且递增。而且小标的增速是一定的，数据的增速只可能大于等于下标。所以可以先通过判断两个端点来确定是否一定有解，如果有解，则通过二分法求解。

#### C++代码

```c++
class Solution {
public:
    int getNumberSameAsIndex(vector<int>& nums) {
        if(nums.empty() || nums[0] > 0 || nums[nums.size() - 1] < nums.size() - 1) return -1;
        int l = 0, r = nums.size() - 1;
        while(l < r){
            int mid = l + r >> 1;
            if(nums[mid] == mid) return mid;
            else if(nums[mid] > mid) r = mid;
            else l = mid + 1;
        }
        if(nums[l] == l) return l;
        else return -1;
    }
};
```

#### Python代码

```python
class Solution(object):
    def getNumberSameAsIndex(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        if len(nums) == 0 or nums[0] > 0 or nums[-1] < len(nums) - 1:
            return -1
        l = 0
        r = len(nums) - 1
        while l < r:
            mid = l + r >> 1
            if nums[mid]  == mid:
                return mid
            elif nums[mid] < mid:
                l = mid + 1
            else:
                r = mid
        return l
```

### 58. 二叉搜索树的第K个节点

[原题链接](https://www.acwing.com/problem/content/66/)

#### 解题思路

二叉树的中序遍历，用栈模拟，弹出的第k个节点就是答案。

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
#include<stack>
class Solution {
public:
    TreeNode* kthNode(TreeNode* root, int k) {
        if(!root) return NULL;
        stack<TreeNode*> st;
        st.push(root);
        while(root -> left){
            st.push(root -> left);
            root = root -> left;
        }
        while(--k){
            auto it = st.top(); st.pop();
            if(it -> right){
                it = it -> right;
                while(it){
                    st.push(it);
                    it = it -> left;
                }
            }
        }
        return st.top();
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
    def kthNode(self, root, k):
        """
        :type root: TreeNode
        :type k: int
        :rtype: TreeNode
        """
        if not root:
            return None
        s = []
        while root:
            s.append(root)
            root = root.left
        i = 0
        while i < k - 1:
            it = s.pop()
            i += 1
            if it.right:
                it = it.right
                while it:
                    s.append(it)
                    it = it.left
        return s.pop()
```

### 59. 二叉树的深度

[原题链接](https://www.acwing.com/problem/content/67/)

#### 解题思路

递归求解即可，数的深度为左子树和右子树深度的最大值加1.

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
    int treeDepth(TreeNode* root) {
        if(!root) return 0;
        return max(treeDepth(root -> left), treeDepth(root -> right)) + 1;
    }
};
```

#### Python代码

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def treeDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return 0
        else:
            return max(self.treeDepth(root.left), self.treeDepth(root.right)) + 1
```

### 60. 平衡二叉树

[原题链接](https://www.acwing.com/problem/content/68/)

#### 解题思路

递归判断两颗子树是不是平衡的，并返回其对应的高度。

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
    int t;
    bool isBalanced(TreeNode* root) {
        if(!root) {
            t = 0;
            return true;
        }
        int ld, rd;
        if(!isBalanced(root -> left)) return false;
        else ld = t;
        if(!isBalanced(root -> right)) return false;
        else rd = t;
        t = max(ld, rd) + 1;
        if(abs(ld - rd) >= 2) return false;
        else return true;
        
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
    def isBalanced(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        if not root:
            self.de = 0
            return True
        ld = 0
        rd = 0
        if not self.isBalanced(root.left):
            return False
        else:
            ld = self.de
        if not self.isBalanced(root.right):
            return False
        else:
            rd = self.de
        if abs(ld - rd) >= 2:
            return False
        else:
            self.de = max(ld, rd) + 1
            return True
```

### 61. 数组中只出现一次的两个数字

[原题链接](https://www.acwing.com/problem/content/69/)

#### 解题思路

两次遍历，然后将所有的数分组进行异或操作即可。

#### C++代码

```c++
class Solution {
public:
    vector<int> findNumsAppearOnce(vector<int>& nums) {
        int t = 0;
        for(auto x:nums) t ^= x;
        int a = 0, b = 0;
        t = t & (-t);
        for(auto x:nums){
            if(x & t) a ^= x;
            else b ^= x;
        }
        vector<int> res;
        res.push_back(a);
        res.push_back(b);
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def findNumsAppearOnce(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        t = 0
        for x in nums:
            t ^= x
        t &= (-t)
        a = 0
        b = 0
        for x in nums:
            if x & t:
                a ^= x
            else:
                b ^= x
        return [a, b]
```

### 62. 数组中唯一只出现一次的数字

[原题链接](https://www.acwing.com/problem/content/70/)

#### 解题思路

1. 统计每一位上，1出现的次数。如果1出现的次数模3余1，则表示结果的这一位是1，否则是零
2. 使用状态机模型。

这道题可以将3推广到任意k。

#### C++代码

```c++
class Solution {
public:
    int findNumberAppearingOnce(vector<int>& nums) {
        int ones = 0, twos = 0;
        for(auto x:nums){
            ones = (ones ^ x) & ~twos;
            twos = (twos ^ x) & ~ones;
        }
        return ones;
    }
};
```

### 63. 和为S的两个数字

[原题链接](https://www.acwing.com/problem/content/71/)

#### 解题思路

先排序，然后双指针法

#### C++代码

```c++
class Solution {
public:
    vector<int> findNumbersWithSum(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        vector<int>res;
        int l = 0, r = nums.size() - 1;
        while(l < r){
            if(nums[l] + nums[r] == target) break;
            else if(nums[l] + nums[r] < target) l ++;
            else r --;
        }
        res.push_back(nums[l]);
        res.push_back(nums[r]);
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def findNumbersWithSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        nums.sort()
        l = 0
        r = len(nums)  -1
        while l < r:
            t = nums[l] + nums[r]
            if t == target:
                break
            elif t > target:
                r -= 1
            else:
                l += 1
        return [nums[l], nums[r]]
```

### 64. 和为S的连续正数序列

[原题链接](https://www.acwing.com/problem/content/72/)

#### 解题思路

双指针算法。如果`i + ... +  j`是一个合法的方法，那么当`i`递增的时候，`j`只可能也递增，就可以使用双指针。所以维护两个指针，往后移动即可。

#### C++代码

```c++
class Solution {
public:
    vector<vector<int> > findContinuousSequence(int sum) {
        int i, j, s;
        vector<vector<int>> res;
        for(int i = 1, j = 1, s = 1; i <= sum; i++){
            while(s < sum){
                j ++;
                s += j;
            }
            if(s == sum && i < j){
                vector<int> tt;
                for(int k = i; k <= j; k ++) tt.push_back(k);
                res.push_back(tt);
            }
            s -= i;
        }
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def findContinuousSequence(self, sum):
        """
        :type sum: int
        :rtype: List[List[int]]
        """
        i = 1
        j = 1
        s = 1
        res =  []
        while i <= sum / 2:
            while s < sum:
                j += 1
                s += j
            if s == sum and i < j:
                t = list(range(i, j + 1))
                res.append(t)
            s -= i
            i += 1
        return res
```

### 65. 翻转单词顺序

[原题链接](https://www.acwing.com/problem/content/73/)

#### 解题思路

直接模拟即可

#### C++代码

```c++
class Solution {
public:
    string reverseWords(string s) {
        int j = 0;
        vector<string> res;
        string t = "";
        while (j < s.size()){
            if(s[j] != ' '){
                t += s[j];
            }else{
                if(t != ""){
                    res.push_back(t);
                }
                t = "";
            }
            j ++;
        }
        if(t != "") res.push_back(t);
        string ans = "";
        for(int i = (int)res.size() - 1; i >= 0; i --){
            ans += res[i];
            if(i != 0) ans += " ";
        }
        return ans;
    }
};
```

#### Python代码

```python
class Solution(object):
    def reverseWords(self, s):
        """
        :type s: str
        :rtype: str
        """
        s = s.strip().split()
        s = s[::-1]
        return " ".join(s)
```

### 66. 左旋转字符串

[原题链接](https://www.acwing.com/problem/content/74/)

#### 解题思路

直接模拟即可。

#### C++代码

```c++
class Solution {
public:
    string leftRotateString(string str, int n) {
        string a = "", b = "";
        for(int i = 0; i < str.size(); i ++){
            if(i < n) a += str[i];
            else b += str[i];
        }
        return b + a;
    }
};
```

#### Pytho代码

```python
class Solution(object):
    def leftRotateString(self, s, n):
        """
        :type s: str
        :type n: int
        :rtype: str
        """
        return s[n:] + s[:n]
```