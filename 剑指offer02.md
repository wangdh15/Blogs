---
title: 剑指offer02
date: 2020-05-02 10:30:23
categories:
	- 数据结构与算法
tags:
	- 剑指offer
Mathjax: true
---

### 12. 机器人运动的范围

[原题链接](https://www.acwing.com/problem/content/22/)

#### 解题思路

图的遍历问题，相当于问图中一共有多少个节点，BFS或者DFS

#### C++代码

```c++
#include<queue>
typedef pair<int,int> PII;
class Solution {
public:
    
    int ths;
    
    bool ok(int a, int b){
        int t = 0;
        while(a){
            t += a % 10;
            a /= 10;
        }
        while(b){
            t += b % 10;
            b /= 10;
        }
        if(t > ths) return false;
        else return true;
    }
    int movingCount(int threshold, int rows, int cols){
        
        ths = threshold;
        if(rows == 0 || cols == 0) return 0;
        queue<PII> q;
        q.push({0, 0});
        int ds[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        bool st[60][60] = {false};
        int ans = 0;
        st[0][0] = true;
        while(q.size()){
            
            auto x = q.front();q.pop();
            ans ++;
            for(int i = 0; i < 4; i++){
                int xx = x.first + ds[i][0];
                int yy = x.second + ds[i][1];
                if(xx >= 0 && xx < rows && yy >= 0 &&  yy < cols && !st[xx][yy] && ok(xx, yy)){
                    st[xx][yy] = true;
                    q.push({xx, yy});
                }
            }
        }
        return ans;
        
    }
};
```

#### Python代码

```python
class Solution(object):
    def movingCount(self, threshold, rows, cols):
        """
        :type threshold: int
        :type rows: int
        :type cols: int
        :rtype: int
        """
        
        def ok(a, b):
            t = 0
            while a:
                t += a % 10
                a //= 10
            while b:
                t += b % 10
                b //= 10
            # print(t)
            if t > threshold:
                return False
            else:
                return True
        if rows == 0 or cols == 0:
            return 0
        
        st = [[False for _ in range(cols)]  for _ in range(rows)]
        t = []
        t.append([0, 0])
        ans = 0
        ds = [[-1, 0], [1, 0], [0, -1], [0, 1]]
        st[0][0] = True
        while t:
            it = t.pop()
            ans += 1
            for i in range(4):
                xx = it[0] + ds[i][0]
                yy = it[1] + ds[i][1]
                if xx >= 0 and xx < rows and  yy >= 0 and yy < cols and not st[xx][yy] and ok(xx, yy):
                    st[xx][yy] = True
                    t.append([xx, yy])
        return ans
```

### 13. 剪绳子

#### 解题思路

1. 动态规划，定义`dp[i]`为长度为`i`的，且剪的段数大于等于2的最大的长度。那么在考虑`i+1`的问题的时候，按照其中某一段进行分类即可。
2. 数学题。结论是尽可能分出多的3，如果余1，则分出一个4，如果余2，则分出一个2.

证明：

- 如果分出的数中有一个大于等于5，则$3*(n_i - 3) \geq n_i$，所以大于5，则拆出来一个3的话会变大
- 所以上面的数中不能包含大于等于5的，所以只可能包含2，3，4。如果包含4的话，可以拆成2， 2 结果不变。所以也可以认为不包含4
- 所以只能包含2， 3。再证明不可能包含多余3个2.因为$2 * 2 * 2 < 3 * 3$
- 所以做多只能包含两个2。当模3余2时，就一个2。模3余1是就两个2.

#### C++代码

```c++
class Solution {
public:
    int maxProductAfterCutting(int length) {
        int dp[60];
        dp[1] = 1;
        dp[2] = 1;
        dp[3] = 2;
        for(int i = 4; i <= length; i ++){
            dp[i] = -1;
            for(int j = 1; j < i; j ++){
                dp[i] = max(dp[i], dp[i - j] * j);
                dp[i] = max(dp[i], (i - j) * j);
            }
        }
        return dp[length];
    }
};

class Solution {
public:
    int maxProductAfterCutting(int length) {
        if(length <= 3) return length = 1;
        int res = 1;
        if(length % 3 == 1) res *= 4, length -= 4;
        if(length % 3 == 2) res *= 2, length -= 2;
        while(length) res *= 3, length -= 3;
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def maxProductAfterCutting(self,length):
        """
        :type length: int
        :rtype: int
        """
        if length <= 3:
            return length - 1
        res = 1
        if length % 3 == 1:
            res *= 4
            length -= 4
        if length % 3 == 2:
            res *= 2
            length -= 2
        while length:
            res *= 3
            length -= 3
        return res
```

### 14. 二进制中1的个数

#### 解题思路

位运算的题目。 `n & (n - 1)`可以将最后一个1抹掉

`n & (- n)`可以将最后一个1取出，也叫`lowbit`操作

#### C++代码

```c++
class Solution {
public:
    int NumberOf1(int n) {
        
        int ans = 0;
        while(n){
            ans ++;
            n &= (n - 1);
        }
        return ans;
    }
};
```

### 15. 数值的整数次方

[原题链接](https://www.acwing.com/problem/content/26/)

#### 解题思路

语法题，直接使用即可。当指数比较大的时候可以使用快速幂。

#### C++代码

```c++
class Solution {
public:

    double qmi(double a, int b){
        if(!b) return 1;
        double res = qmi(a, b >> 1);
        res = res * res;
        if(b & 1) res *= a;
        return res;
    }

    double Power(double ba, int ex) {
        
        if(ex == 0) return 1;
        if(ba == 0) return 0;
        bool minus = ex < 0;
        ex = abs(ex);
        double res = qmi(ba, ex);
        if(minus) return 1.0 / res;
        else return res;
    }
};
```

#### Python 代码

```python
class Solution(object):
    def Power(self, base, exponent):
        """
        :type base: float
        :type exponent: int
        :rtype: float
        """
        res = 1
        for i in range(abs(exponent)):
            res *= base
        if exponent < 0:
            return 1 / res
        else:
            return res
```

### 15.在O(1)时间删除链表节点

[原题链接](https://www.acwing.com/problem/content/85/)

#### 解题思路

不需要真的删除，只需要将下一个节点`copy`过来即可。

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
class Solution {
public:
    void deleteNode(ListNode* node) {
        
        node -> val = node -> next -> val;
        node -> next = node -> next -> next;
        
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
    def deleteNode(self, node):
        """
        :type node: ListNode
        :rtype: void 
        """
        node.val = node.next.val
        node.next = node.next.next
```

### 16. 删除链表中重复的节点

[原题链接](https://www.acwing.com/problem/content/27/)

#### 解题思路

双指针法。

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
class Solution {
public:
    ListNode* deleteDuplication(ListNode* head) {
        
        ListNode* h = new ListNode(-1);
        h -> next = head;
        ListNode* p = h;
        while(p -> next){
            ListNode* q = p -> next;
            while(q && q -> val == p -> next -> val) q = q -> next;
            if(q == p -> next -> next) p = p -> next;
            else p -> next = q;
        }
        return h -> next;
        
    }
};
```

### 17.

### 18.

### 19. 调整整数顺序使奇数位于偶数前面

[原题链接](https://www.acwing.com/problem/content/30/)

#### 解题思路

双指针，和快排的分段部分有点类似

```c++
class Solution {
public:
    void reOrderArray(vector<int> &array) {
        int l = 0, r = array.size() - 1;
        while(l < r){
            while(l <= r && array[l] & 1) l ++;
            while(r >= l && !(array[r] & 1)) r --;
            if(l < r) swap(array[l], array[r]);
        }
    }
};
```

```python
class Solution(object):
    def reOrderArray(self, array):
        """
        :type array: List[int]
        :rtype: void
        """
        l = 0
        r = len(array) - 1
        while l < r:
            while l <= r and  array[l] & 1:
                l += 1
            while r >= l and  not (array[r]  & 1):
                r -= 1
            if l < r:
                array[l], array[r] = array[r], array[l]
                l += 1
                r -= 1
```

### 20. 链表中倒数第k个节点

[原题链接](https://www.acwing.com/problem/content/32/)

#### 解题思路

快慢指针法

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
class Solution {
public:
    ListNode* findKthToTail(ListNode* pListHead, int k) {
        ListNode* p = pListHead;
        if(k == 0) return NULL;
        if(!pListHead) return NULL;
        for(int i = 2; i <= k; i ++){
            if(p -> next) p = p -> next;
            else return NULL;
        }
        ListNode* q = pListHead;
        while(p -> next){
            p = p -> next;
            q = q -> next;
        }
        return q;
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
    def findKthToTail(self, pListHead, k):
        """
        :type pListHead: ListNode
        :type k: int
        :rtype: ListNode
        """
        if not pListHead:
            return None
        if k == 0:
            return None
        p = pListHead
        for i in range(2, k + 1):
            if p.next:
                p = p.next
            else:
                return None
        q = pListHead
        while p.next:
            p = p.next
            q = q.next
        return q
```

### 21. 链表中环的入口节点

[原题链接]()

#### 解题思路

1. 朴素解法，沿着链表遍历链表，并将经历过的节点加入到集合中。当遇到第一个遍历过的节点时，返回这个节点
2. 

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
    ListNode *entryNodeOfLoop(ListNode *head) {
        unordered_set<ListNode*> se;
        if(!head) return NULL;
        while(head){
            if(se.count(head)) return head;
            else se.insert(head), head = head -> next;
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
    def entryNodeOfLoop(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        se = set()
        if not head:
            return None
        while head:
            if head in se:
                return head
            else:
                se.add(head)
                head = head.next
        return None
        
```