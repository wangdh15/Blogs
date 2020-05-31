---
title: 剑指offer05
date: 2020-05-06 18:57:07
categories:
	- 数据结构与算法
tags:
	- 剑指offer

mathjax: true
---

### 45.数字序列中某一位的数字

[原题链接](https://www.acwing.com/problem/content/52/)

#### 解题思路

#### C++代码

```c++

```

#### Python代码

```python

```

### 46. 把数组排成最小的数

[原题链接](https://www.acwing.com/problem/content/54/)

#### 解题思路

和最短等待时间中的相类似。将所有的数据排序，排序的原则是，如果相互交换之后，会使结果变大。那么就需要保留较小的次序。可用反证法，如果相邻两个不满足条件的话，相互交换之后会使值更小。

#### C++代码

```c++
class Solution {
public:

    static bool cmp(int a, int b){   // 认为定义cmp
        string as = to_string(a), bs = to_string(b);
        return as + bs < bs + as;
    }
    
    string printMinNumber(vector<int>& nums) {
        sort(nums.begin(), nums.end(), cmp);
        string res;
        for(auto x: nums){
            res += to_string(x);
        }
        return res;
    }
};
```

#### Python代码

```python
import functools
class Solution(object):
    def printMinNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: str
        """
        def cmp(a, b):
            if str(a) + str(b) < str(b) + str(a):
                return -1
            else:
                return 1
        
        nums.sort(key=functools.cmp_to_key(cmp))
        return "".join([str(x) for x in nums])
```

### 47. 把数字翻译成字符串

[原题链接](https://www.acwing.com/problem/content/55/)

#### 解题思路

动态规划。定义状态`dp[i]`为以`i`结尾的字符串的解析个数。则`dp[i+1]`为

- 第`i+1`位字符单独解析,`dp[i]`
- 第`i+1`为和第`i`位一起，且数字不大于25,且第`i`为不是零`dp[i-1]`

将上述两种情况相加即可。

#### C++代码

```c++
class Solution {
public:
    int getTranslationCount(string s) {
        s = " " + s;
        vector<int> dp = vector<int>(s.size(), 0);
        dp[0] = 1;
        for(int i = 1; i < s.size(); i ++){
            dp[i] = dp[i - 1];
            if((s[i - 1] - '0') * 10 + s[i] - '0' <= 25 && s[i-1] != '0') dp[i] += dp[i - 2]; 
        }
        return dp[s.size() - 1];
    }
};
```

#### Python代码

```python
class Solution:
    def getTranslationCount(self, s):
        """
        :type s: str
        :rtype: int
        """
        s = " " + s
        dp = [1] * len(s)
        for i in range(2, len(s)):
            dp[i] = dp[i-1];
            if int(s[i-1:i+1]) <= 25 and s[i-1] != "0":
                dp[i] += dp[i-2]
        return dp[-1]
```

###48. 礼物的最大价值

[原题链接](https://www.acwing.com/problem/content/56/)

#### 解题思路

数字三角形类型的动态规划。设状态`dp[i][j]`为从`i, j` 处开始的最大，则

$$dp[i][j] = grid[i][j] + max(d[i+1][j], dp[i][j+1])$$

将边界向外扩充一行一列，填充零，处理边界条件。

#### C++代码

```c++
class Solution {
public:
    int getMaxValue(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();
        vector<vector<int>> dp = vector<vector<int>>(n + 1, vector<int>(m + 1));
        for(int i = n -1; i >= 0; i --){
            for(int j = m - 1; j >= 0; j --){
                dp[i][j] = grid[i][j] + max(dp[i+1][j], dp[i][j+1]);
            }
        }
        return dp[0][0];
    }
};
```

#### Python代码

```python
class Solution(object):
    def getMaxValue(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        n = len(grid)
        m = len(grid[0])
        dp = [[0 for _ in range(m + 1)] for _ in range(n + 1)]
        for i in range(n - 1, -1, -1):
            for j in range(m - 1, -1, -1):
                dp[i][j] = grid[i][j] + max(dp[i+1][j], dp[i][j+1]);
                
        return dp[0][0];
```

### 49. 最长不含重复字符的子字符串

[原题链接](https://www.acwing.com/problem/content/57/)

#### 解题思想

#### C++代码

```c++
#include<unordered_map>
class Solution {
public:
    int longestSubstringWithoutDuplication(string s) {
        unordered_map<char, int> ma;
        int res = 0;
        int i = 0;
        int j = 0;
        while(j < s.size()){
            ma[s[j]] ++;
            if(ma[s[j]] == 1){
                res = max(res, j - i + 1);
            }else{
                while(i < j && ma[s[j]] != 1){
                    ma[s[i]] --;
                    i ++;
                }
                res = max(res, j - i + 1);
            }
            j ++;
        }
        return res;
    }
};
```

#### Python代码

```python
class Solution:
    def longestSubstringWithoutDuplication(self, s):
        """
        :type s: str
        :rtype: int
        """
        i = 0
        j = 0
        res = 0
        r = dict()
        while j < len(s):
            if s[j] not in r:
                r[s[j]] = 1
            else:
                r[s[j]] += 1

            while i < j and r[s[j]] > 1:
                r[s[i]] -= 1
                i += 1
            res = max(res, j - i + 1)
            j += 1
        return res
```

### 50. 丑数

[原题链接](https://www.acwing.com/problem/content/58/)

#### 解题思路

1. 优先级队列加字典。拿到一个丑数就将这个丑数乘2 3 5 的结果放到队列中，如果曾经放过就不放了，所以需要用一个set。
2. 分别维护三个序列。TODO

#### C++代码

```c++
// 优先级队列的解法
#include<queue>
#include<unordered_set>
class Solution {
public:
    int getUglyNumber(int n) {
        priority_queue<int, vector<int>, greater<int>> q;
        q.push(1);
        unordered_set<int> se;
        se.insert(1);
        int cnt = 1;
        while(cnt < n){
            auto it = q.top();
            q.pop();
            if(!se.count(it * 2)){
                q.push(it * 2);
                se.insert(it * 2);
            }
            if(!se.count(it * 3)){
                q.push(it * 3);
                se.insert(it * 3);
            }
            if(!se.count(it * 5)){
                q.push(it * 5);
                se.insert(it * 5);
            }
            cnt ++;
        }
        return q.top();
    }
};
// 数学的解法
class Solution {
public:
    int getUglyNumber(int n) {
        vector<int> q;
        q.push_back(1);
        int i = 0, j = 0, k = 0;
        while(--n){
            int t = min(q[i]  *2, min(q[j] * 3, q[k] * 5));
            q.push_back(t);
            if(q[i] * 2 == t) i ++;
            if(q[j] * 3 == t) j ++;
            if(q[k] * 5 == t) k ++;
        }
        return q.back();
    }
};
```

#### Python代码

```python
class Solution(object):
    def getUglyNumber(self, n):
        """
        :type n: int
        :rtype: int
        """
        q = [1]
        i = 0
        j = 0
        k = 0
        while len(q) < n:
            t = min(q[i] * 2, q[j] * 3, q[k] * 5)
            q.append(t)
            if q[i]  * 2 == t:
                i += 1
            if q[j] * 3 == t:
                j += 1
            if q[k] * 5 == t:
                k += 1
        return q[-1]
```

### 51. 字符串中第一个只出现一次的字符

[原题链接](https://www.acwing.com/problem/content/59/)

#### 解题思路

两次遍历，第一次用hash表存储每个字符出现的次数，然后第二次遍历输出第一个次数为1的字符。

#### C++代码

```c++
#include<unordered_map>
class Solution {
public:
    char firstNotRepeatingChar(string s) {
        unordered_map<char, int> ma;
        for(auto x:s) ma[x] ++;
        for(auto x:s){
            if(ma[x] == 1) return x;
        }
        return '#';
    }
};
```

####Python代码

```python
class Solution:
    def firstNotRepeatingChar(self, s):
        """
        :type s: str
        :rtype: str
        """
        d = dict()
        for x in s:
            if x in d:
                d[x] += 1
            else:
                d[x] = 1
        for x in s:
            if d[x] == 1:
                return x
        return "#"
```

### 52. 字符流中第一个只出现一次的字符

[原题链接](https://www.acwing.com/problem/content/60/)

#### 解题思路

使用队列维护输入的数据，并使用字典记录数据中各个字符出现的次数。输出的时候，逐个pop队列顶端的出现次数大于1的字符，然后向后移动。

#### C++代码

```c++
#include<unordered_map>
#include<queue>
class Solution{
public:
    unordered_map<char, int> ma;
    queue<char> q;
    //Insert one char from stringstream
    void insert(char ch){
        ma[ch] ++;
        if(ma[ch] == 1){   // 如果字符出现的次数已经大于1，则不加入队列
            q.push(ch);
        }

    }
    //return the first appearence once char in current stringstream
    char firstAppearingOnce(){
        while(q.size() && ma[q.front()] > 1) q.pop();
        if(q.size()) return q.front();
        else return '#';
    }
};
```

#### Python代码

```python
class Solution:  
    
    def __init__(self):
        self.data = []
        self.rec = dict()
    
    def firstAppearingOnce(self):
        """
        :rtype: str
        """
        while self.data and self.rec[self.data[0]] > 1:
            self.data.pop(0)
        if self.data:
            return self.data[0]
        else:
            return "#"
        
        
    def insert(self, char):
        """
        :type char: str
        :rtype: void
        """
        if char in self.rec:
            self.rec[char] += 1
        else:
            self.rec[char] = 1
        self.data.append(char)
        
```

### 53. 数组中的逆序对

[原题链接](https://www.acwing.com/problem/content/61/)

#### 解题思路

归并排序统计逆序对数

#### C++代码

```c++
class Solution {
public:
    
    vector<int> tmp, q;
    int res = 0;
    
    void ms(int l, int r){
        if(l >= r) return ;
        int mid = l + r >> 1;
        ms(l, mid);
        ms(mid + 1, r);
        int i = l, j = mid + 1, k = 0;
        while(i <= mid && j <= r){
            if(q[i] <= q[j]) tmp[k++] = q[i ++];
            else{
                res += (mid - i + 1);
                tmp[k++] = q[j ++];
            }
        }
        while(i <= mid) tmp[k++] = q[i++];
        while(j <= r) tmp[k++] = q[j++];
        for(int i  = l, k  = 0; i <= r; i ++, k ++){
            q[i] = tmp[k];
        }
    }
    
    int inversePairs(vector<int>& nums) {
        tmp = vector<int>(nums.size());
        q = nums;
        ms(0, nums.size() - 1);
        // for(auto x:q) cout << x << " ";
        // cout << endl;
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def inversePairs(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        
        global q
        global t
        global res
        res = 0
        q = nums
        t = [0] * len(nums)
        
        def ms(l, r):
            global q
            global t
            global res
            if l >= r:
                return
            mid = l + r >> 1
            ms(l, mid)
            ms(mid + 1, r)
            i = l
            j = mid + 1
            k = 0
            while i <= mid and  j <= r:
                if q[i] <= q[j]:
                    t[k] = q[i]
                    i += 1
                else:
                    t[k] = q[j]
                    res += (mid - i + 1)
                    j += 1
                k += 1
            while i <= mid:
                t[k] = q[i]
                i += 1
                k += 1
            while j <= r:
                t[k] = q[j]
                k += 1
                j += 1
            k = 0
            for i in range(l, r + 1):
                q[i] = t[k]
                k += 1
        
        ms(0, len(nums) - 1)
        return res
```

### 54. 两个链表的第一个公共结点

[原题链接](https://www.acwing.com/problem/content/62/)

#### 解题思路

1. 先遍历一个链表，使用set存储节点，然后再扫描另一个链表
2. 双指针法，一个到了末尾就指向另一个的起点，直到两个相遇

#### C++代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
#include<unordered_set>
class Solution {
public:
    ListNode *findFirstCommonNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode*> se;
        while(headA){
            se.insert(headA);
            headA = headA -> next;
        }
        while(headB){
            if(se.count(headB)) return headB;
            headB = headB -> next;
        }
        return NULL;
    }
};

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *findFirstCommonNode(ListNode *headA, ListNode *headB) {
        
        bool flag1 = false, flag2 = false;
        if(!headA || !headB) return NULL;
        ListNode *p = headA, *q = headB;
        while(true){
            if(p == q) return p;
            if(p -> next){
                p = p -> next;
            }else{
                if(!flag1) flag1 = true, p = headB;
                else return NULL;
            }
            if(q -> next){
                q = q -> next;
            }else{
                if(!flag2) flag2 = true, q = headA;
                else return NULL;
            }
        }
        return NULL;
    }
};
```

#### Python代码

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def findFirstCommonNode(self, headA, headB):
        """
        :type headA, headB: ListNode
        :rtype: ListNode
        """
        if not headA or not headB:
            return None
        f1 = False
        f2 = False
        p = headA
        q = headB
        while True:
            if p == q:
                return p
            if p.next:
                p = p.next
            else:
                if not f1:
                    p = headB
                    f1 = True
                else:
                    return None
            if q.next:
                q = q.next
            else:
                if not f2:
                    f2 = True
                    q = headA
                else:
                    return None
```

###  55. 数字在排序数组中出现的次数

[原题链接](https://www.acwing.com/problem/content/description/63/)

#### 解题思路

二分，找到区间的左右端点即可。

#### C++代码

```c++
class Solution {
public:
    int getNumberOfK(vector<int>& nums , int k) {
        if(nums.size() == 0) return 0;
        int l = 0, r = nums.size() - 1;
        while(l < r){
            int mid = l + r >> 1;
            if(nums[mid] < k) l = mid + 1;
            else r = mid;
        }
        if(nums[l] != k) return 0;
        int ll = l;
        r = nums.size() - 1;
        while(l < r){
            int mid = l + r + 1>> 1;
            if(nums[mid] > k) r = mid - 1;
            else l = mid;
        }
        return r - ll + 1;
    }
};
```

#### Pyhton代码

```python
class Solution(object):
    def getNumberOfK(self, nums, k):
        """
        :type nums: list[int]
        :type k: int
        :rtype: int
        """
        if len(nums) == 0:
            return 0
        l = 0
        r = len(nums) - 1
        while l < r:
            mid = l + r >> 1
            if nums[mid] < k:
                l = mid  +1;
            else:
                r = mid
        if nums[l] != k:
            return 0
        ll = l
        r = len(nums) - 1
        while l < r:
            mid = l + r + 1>> 1
            if nums[mid] > k:
                r = mid - 1
            else:
                l = mid
        return r - ll + 1
```

