---
title: 剑指offer04
date: 2020-05-05 23:41:22
categories:
	- 数据结构与算法
tags:
	- 剑指offer
Mathjax: true
---

### 34. 二叉搜索树的后续遍历序列

[原题链接](https://www.acwing.com/problem/content/44/)

#### 解题思路

从前到后扫描找到第一个不小于根的元素，从后向前找到第一个不大于根的元素。

如果刚好将区间划分为二，说明是可以的。然后递归求解即可。

#### C++代码

```c++
class Solution {
public:
    
    vector<int> data;
    
    bool ok(int l, int r){
        if(l >= r) return true;
        int i = l;
        while(i <= r - 1 && data[i] < data[r]) i ++;  // 寻找第一个大于根的元素
        int j = r - 1;
        while(j >= l && data[j] > data[r]) j --;  // 寻找第一个小于根的元素
        if( i - j  != 1) return false;
        else return ok(l, j) && ok(i, r - 1);
    }
    
    bool verifySequenceOfBST(vector<int> seq) {
        data = seq;
        return ok(0, seq.size() - 1);
        
    }
    
};
```

#### Python代码

```python
class Solution:
    def verifySequenceOfBST(self, seq):
        """
        :type sequence: List[int]
        :rtype: bool
        """
        def ok(l ,r):
            if l >= r:
                return True
            i = l
            while i <= r - 1 and seq[i] < seq[r]:
                i += 1
            j = r - 1
            while j >= l and seq[j] > seq[r]:
                j -= 1
            if i - j != 1:
                return False
            else:
                return ok(l, j) and ok(i, r - 1);
        return ok(0, len(seq) - 1)
```

### 35. 二叉树中和为某一值的路径

[原题链接](https://www.acwing.com/problem/content/45/)

#### 解题思路

二叉树的深度优先遍历

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
    
    vector<int> tmp;
    int sum = 0, target;
    vector<vector<int>> res;
    void dfs(TreeNode* n){
        tmp.push_back(n -> val);
        sum += n -> val;
        if(!n -> left &&  !n -> right){
            if (sum == target) res.push_back(tmp);
        }
        if(n-> left) dfs( n -> left);
        if(n -> right) dfs(n -> right);
        sum -= n -> val;
        tmp.pop_back();
        return ;
    }
    vector<vector<int>> findPath(TreeNode* root, int s) {
        target = s;
        if(!root) return res;
        dfs(root);
        return res;
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
    def findPath(self, root, sum):
        """
        :type root: TreeNode
        :type sum: int
        :rtype: List[List[int]]
        """
        tmp = []
        res = []
        global t_s
        t_s = 0
        def dfs(n):
            global t_s
            tmp.append(n.val)
            t_s += n.val
            if not n.left and not n.right and t_s == sum:
                res.append(tmp[:])
            if n.left:
                dfs(n.left)
            if n.right:
                dfs(n.right)
            tmp.pop()
            t_s -= n.val

        if not root:
            return []
        dfs(root)
        return res
```

### 36.复杂链表的复制

[原题链接](https://www.acwing.com/problem/content/89/)

#### 解题思路

- 先使用hash表，将原始节点作为key，然后复制一份新的节点作为value，然后遍历，将复制的节点之间相互连接起来
- 将链表的每个节点复制一份，然后整个链表就没有断，同时从一个节点也能够在`O(1)`的时间找到当前节点的复制的节点。所以达到了和上面的一样的信息。然后先将`random`域分配给复制的节点，然后将`next`域赋正确的值，并将原始链表复原

#### C++代码

```c++
// 用hash表存储原始节点和复制节点的映射关系
/**
 * Definition for singly-linked list with a random pointer.
 * struct ListNode {
 *     int val;
 *     ListNode *next, *random;
 *     ListNode(int x) : val(x), next(NULL), random(NULL) {}
 * };
 */
#include<unordered_map>
class Solution {
public:
    ListNode *copyRandomList(ListNode *head) {
        unordered_map<ListNode*, ListNode*> ma;
        ListNode *cur = head;
        while(cur){
            ListNode *t = new ListNode(cur -> val);
            ma[cur] = t;
            cur = cur -> next;
        }
        cur = head;
        while(cur){
            if(cur -> next) ma[cur] -> next = ma[cur -> next];
            if(cur -> random) ma[cur] -> random = ma[cur -> random];
            cur = cur -> next;
        }
        return ma[head];
    }
};

// 将每个节点复制到其下一个节点。
/**
 * Definition for singly-linked list with a random pointer.
 * struct ListNode {
 *     int val;
 *     ListNode *next, *random;
 *     ListNode(int x) : val(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *copyRandomList(ListNode *head) {
        ListNode *cur = head;
        if(!head) return NULL;
        while(cur){
            ListNode *t = new ListNode(cur -> val);
            t -> next = cur -> next;
            cur -> next = t;
            cur = t -> next;
        }
        
        cur = head;
        while(cur){
            if(cur -> random) cur -> next -> random = cur -> random -> next;
            cur = cur -> next -> next;
        }
        cur = head;
        ListNode *res = head -> next;
        while(cur){
            ListNode *t = cur -> next;
            cur -> next = t -> next;
            cur = cur -> next;
            if(t -> next) t -> next = t -> next -> next;
        }
        return res;
    }
};
```

#### Python代码

```python
# Definition for singly-linked list with a random pointer.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None
#         self.random = None
class Solution(object):
    def copyRandomList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head:
            return None
        cur = head
        while cur:
            t = ListNode(cur.val)
            t.next = cur.next
            cur.next = t
            cur = t.next
        cur = head
        while cur:
            if cur.random:
                cur.next.random = cur.random.next
            cur = cur.next.next
        res = head.next
        cur = head
        while cur:
            t = cur.next
            cur.next = t.next
            cur = cur.next
            if t.next:
                t.next = t.next.next
        return res
```

###37.  二叉搜索树与双向链表

[原题链接]()

#### 解题思路

递归。同时存储处理结果的`left`和`right`。

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
    
    TreeNode *left, *right;
    
    void f(TreeNode* n){
        if(!n -> left && !n -> right) {
            left = n;
            right = n;
            return;
        }else{
            TreeNode *lt;
            if(n -> left){  // 递归处理左子树
                f( n -> left);
                right -> right = n;
                n -> left = right;
                lt = left;
            }else{
                lt = n;
            }
            if(n -> right){  // 递归处理右子树
                f(n -> right);
                left -> left = n;
                n -> right = left;
            }else{
                right = n;
            }
            left = lt;
        }
    }
    
    TreeNode* convert(TreeNode* root) {
        if(!root) return NULL;
        f(root);
        return left;
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
    def convert(self, root):
        """
        :type root: TreeNode
        :rtype: TreeNode
        """
        if not root:
            return None
        global left
        global right
        left = None
        right = None
        def f(n):
            global left
            global right
            if not n.left and not n.right:
                left = n
                right = n
                return
            else:
                lt = None
                if n.left:
                    f(n.left)
                    right.right = n
                    n.left = right
                    lt = left
                else:
                    lt = n
                if n.right:
                    f(n.right)
                    left.left = n
                    n.right = left
                else:
                    right = n
                left = lt
        f(root)
        return left
```

###  38. 序列化二叉树

[原题链接](https://www.acwing.com/problem/content/46/)

#### 解题思路

存储数的层序遍历的结果，并将第一个None叶子节点存储进去。

`leetcode`的输入数据格式

#### Pyhton代码

```python
# Definition for a binary tree node.
class TreeNode(object):
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:

    def serialize(self, root):
        """Encodes a tree to a single string.

        :type root: TreeNode
        :rtype: str
        """
        res = []
        t = []
        t.append(root)
        while t:
            it = t.pop(0)
            if it != None:
                res.append(it.val)
                if it.left:
                    t.append(it.left)
                else:
                    t.append(None)
                if it.right:
                    t.append(it.right)
                else:
                    t.append(None)
            else:
                res.append(None)
        return str(res)

    def deserialize(self, data):
        """Decodes your encoded data to tree.

        :type data: str
        :rtype: TreeNode
        """
        data = [None if x.strip() == "None" else int(x) for x in data[1:-1].split(",")]
        data = [TreeNode(x) if x != None else None for x in data]
        if data[0] == None:
            return None
        i = 0  # 根的下标
        j = 1 #  两个叶子的下标
        while j < len(data):
            data[i].left = data[j]
            data[i].right = data[j + 1]
            i += 1
            while i <= j and data[i] == None:
                i += 1
            if not data[i]:
                break
            j += 2
        return data[0]
```

### 39. 数字排列

[原题链接](https://www.acwing.com/problem/content/47/)

#### 解题思路

主要的难点是需要对重复数字的排列进行去重。一般DFS的思路是每一步判断每个位置能够放的元素。

这个题目可以换一个想法。我们不再从位置的角度考虑，而是从需要排序的元素考虑。

首先将所有的输入数据进行排序，这样相同的数字就在一起了。然后考虑第一个数摆放的位置。确定了第一个数的位置之后，

第二个数的可以摆放的位置的起点有两类：

- 如果和第一个数字不同的话，则可以从0开始摆放
- 如果和第一个数字相同的话，则人为对重复数字进行一个定序，则第二个数字只能从第一个数字放置的位置的下一个位置开始放置。这样就能够做到不重复。

#### C++代码

```c++
class Solution {
public:
    vector<int> rec, nu;
    vector<bool> st;
    vector<vector<int>> ans;
    
    void dfs(int li, int bi){
        if(li == nu.size()){
            ans.push_back(rec);
            return ;
        }
        for(int i = bi; i < nu.size(); i ++){
            if(!st[i]){
                st[i] = true;
                rec[i] = nu[li];
                if(li + 1 < nu.size() && nu[li + 1] == nu[li]){
                    dfs(li + 1, i + 1);
                }else{
                    dfs(li + 1, 0);
                }
                st[i] = false;
            }
        }
    }
    
    vector<vector<int>> permutation(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        st = vector<bool>(nums.size(), false);
        nu = nums;
        rec = vector<int>(nums.size());
        dfs(0, 0);
        return ans;
    }
};
```

#### Pyhton代码

```python
class Solution:
    def permutation(self, nu):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        def dfs(li, bi):
            global nums
            global rec
            global ans
            global st
            if li == len(nums):
                ans.append(rec[:])
            for i in range(bi, len(nums)):
                if not st[i]:
                    st[i] = True
                    rec[i] = nums[li]
                    if li + 1 < len(nums) and nums[li + 1] == nums[li]:
                        dfs(li + 1, i + 1)
                    else:
                        dfs(li + 1, 0)
                    st[i] = False
                
        global rec
        global ans
        global st
        global nums
        nu.sort()
        nums = nu
        rec = [-1]  * len(nums)
        ans = []
        st = [False]  * len(nums)
        dfs(0, 0)
        
        return ans
```

### 40. 数组中出现次数超过一半的数字

[原题链接](https://www.acwing.com/problem/content/48/)

#### 解题思路

摩尔投票法求众数。

在数据中随机算两个不同的数进行消耗，那么最后剩下的数一定是众数。最坏的情况下也是所有其他的树和众数消耗。

- cnt 为零时，val = x
- cnt不为零时
  - 如果val == x ,cnt ++
  - 否则val --;

时间复杂度`O(n)`，空间复杂度`O(1)`

#### C++代码

```c++
class Solution 
public:
    int moreThanHalfNum_Solution(vector<int>& nums) {
        int cnt = 0, val = -1; 
        for(auto x:nums){
            if(cnt == 0){
                val = x;
                cnt  = 1;
            }else{
                if(val == x){
                    cnt ++;
                }else{
                    cnt --;
                }
            }
        }
        return val;
    }
};
```

#### Python代码

```python
class Solution(object):
    def moreThanHalfNum_Solution(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        cnt = 0
        val = -1
        for x in nums:
            if cnt == 0:
                val = x
                cnt += 1
            else:
                if val == x:
                    cnt += 1
                else:
                    cnt -= 1
        return val
```

### 41. 最小的k个数

[原题链接](https://www.acwing.com/problem/content/49/)

#### 解题思路

1. 直接对所有的数据排序时间复杂度`O(n log n)`
2. 使用大根堆，维护一个大小为k的堆，然后插入一个元素之后就将堆中的最大元素删除。

#### C++代码

```c++
#include<queue>
#include<algorithm>
class Solution {
public:
    vector<int> getLeastNumbers_Solution(vector<int> input, int k) {
        priority_queue<int> heap;
        for(auto x:input){
            heap.push(x);
            if(heap.size() > k){
                heap.pop();
            }
        }
        vector<int> res;
        while(heap.size()){
            res.push_back(heap.top());
            heap.pop();
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```

#### Pyhton代码

```python
import heapq
class Solution(object):
    def getLeastNumbers_Solution(self, input, k):
        """
        :type input: list[int]
        :type k: int
        :rtype: list[int]
        """
        a = []
        for x in input:
            heapq.heappush(a, -x)
            if len(a) > k:
                heapq.heappop(a)
        
        a = [-x for x in a]
        a.sort()
        return a
```

### 42. 数据流中的中位数

[原题链接](https://www.acwing.com/problem/content/88/)

#### 解题思路

使用两个堆，一个大根维维护前面一半的数，一个小根堆维护后面一半的数。每次来了一个数，判断其应该放到哪个堆里面。

同时保证两个堆的`size()`相差不超过1.

**注意：集合的size函数返回的是无符号数，做减法出现复数会溢出。需要先转为int**

#### C++代码

```c++
class Solution {
public:

    priority_queue<int> left;
    priority_queue<int, vector<int>, greater<int>> right;
    void insert(int num){
        if(!left.size() && !right.size()) right.push(num);
        else if(!left.size() && right.size()){
            if(num <= right.top()){
                left.push(num);
            }else{
                right.push(num);
                left.push(right.top());
                right.pop();
            }
        }else{
            if(num > right.top()){
                right.push(num);
            }else{
                left.push(num);
            }
            while((int)left.size() - (int)right.size() >= 2){
                right.push(left.top());
                left.pop();
            }
            while((int)right.size() - (int)left.size() >= 2){
                left.push(right.top());
                right.pop();
            }
        }
    }

    double getMedian(){
        if(left.size() == right.size()) return (double)(left.top() + right.top()) / 2;
        else if(left.size() > right.size()) return (double)left.top();
        else return (double) right.top();
    }
};
```

### 43. 连续子数组的最大和

[原题链接](https://www.acwing.com/problem/content/50/)

#### 解题思路

动态规划，定义`dp[i]`为以第`i`个元素结尾的子数组和的最大值。那么有如下转移方程

- 如果`dp[i-1] <= 0`,则` dp[i] = nums[i]`
- 否则，`dp[i] = dp[i-1] + nums[i]`

#### C++代码

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        vector<int> dp = vector<int>(nums.size());
        int res = -1e9;
        for(int i = 0; i < nums.size(); i ++){
            if(!i || dp[i-1] < 0) dp[i] = nums[i];
            else dp[i] = dp[i-1] + nums[i];
            res = max(res, dp[i]);
        }
        return res;
    }
};
```

#### Python代码

```python
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        dp = [0] * len(nums)
        res = float("-inf")
        for i in  range(len(nums)):
            if not i or dp[i-1] <= 0:
                dp[i] = nums[i]
            else:
                dp[i] = dp[i-1] + nums[i]
            res = max(res, dp[i])
        return res
```

### 44. 从1到n整数中1出现的次数

[原题链接](https://www.acwing.com/problem/content/51/)

#### 解题思路

数位统计DP。待补

#### C++代码

#### Python代码

