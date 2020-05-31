---
title: 剑指offer01
date: 2020-05-01 14:16:22
categories:
	- 数据结构与算法	
tags:
	- 剑指offer
mathjax: true
---

### 01. 找出数组中重复的数字

[原题链接](https://www.acwing.com/problem/content/14/)

#### 解题思路

1. 首先扫描所有的数据，如果发现有越界的情况，则直接返回-1即可。然后第二次扫描的过程中利用集合记录某个元素是否出现过，如果出现了，则返回这个元素。这个解法的时间复杂度为`O(n)`, 但是空间复杂度不是`O(1)`
2. 第二种方法是使用空间复杂度为`O(1)`的方法。其实可以将一个数组看做一个`map`，只不过`key`就是数组的下标，`value`就是其对应的位置存储的值。第一遍扫描判断出界的情况。第二次扫描的时候，如果发现下标`i`位置的数不是`i`,则将`nums[i]`放到下标为`nums[i]`的位置，即将其放到其正确的位置，以保证`i`的位置是`i`，并将下标为`nums[i]`位置的元素放到`i`位置，一直这种重复下去，直到找到为元素`i`，或者发现了冲突，即`nums[i]`位置的元素是`nums[i]`,也`i`的位置的元素也是`nums[i]`，即可返回这个元素。由于每个元素只会被放到其正确的位置依次，所以时间复杂度为`O(n)`,空间复杂度为`O(1)`。

#### c++代码

```c++
//方法1
#include<unordered_set>
class Solution {
public:
    int duplicateInArray(vector<int>& nums) {
        
        unordered_set<int> se;
        int res = -1;
        for(auto x:nums){
            if(x < 0 || x > nums.size() - 1) {
                res = -1;
                break;
            }
            if(se.count(x)) res = x;
            else se.insert(x);
        }
        return res;
    }
};

// 方法2
class Solution {
public:
    int duplicateInArray(vector<int>& nums) {
        
        int n = nums.size();
        for(auto x:nums){
            if(x < 0 || x >= n) return -1;
        }
        for(int i = 0; i < n; i ++){
            while(nums[i] != i && nums[nums[i]] != nums[i]) swap(nums[i], nums[nums[i]]);
            if(nums[i] != i) return nums[i];
        }
        return -1;
    }
};
```

#### Python 代码

```python
// 方法2
class Solution(object):
    def duplicateInArray(self, nums):
        """
        :type nums: List[int]
        :rtype int
        """
        n = len(nums)
        for x in nums:
            if x < 0 or x >= n:
                return -1
        
        for i in range(n):
            while nums[i] != i and nums[nums[i]] != nums[i]:
                x = nums[i]  ##  不可以nums[i], nums[nums[i]] = nums[nums[i]], nums[i]这样也会先执行第一句再执行第二句，而不是并行执行。
                y = nums[nums[i]]
                nums[x] = x
                nums[i] = y;
            if nums[i] != i:
                return nums[i];
        return -1;
```

### 02. 不修改数组找出重复的数字

[原题链接](https://www.acwing.com/problem/content/15/)

要求只是用`O(1)`的额外空间，而且数组的个数为`n+1`，数字的范围为`n`，所以可以使用容斥原理加二分的方法。

将数的取值范围划分为两半，统计分别落入两个范围的数字的个数，一定有一个区间的数字个数大于区间的长度，那么这里面一定存在着一个重复的数字。如下下去不断将范围二分，直到最后找到答案。

因为一共需要`log n`次遍历，所以时间复杂度为`O(nlong n)`。空间复杂度为`O(1)`。

#### C++代码

```c++
class Solution {
public:
    int duplicateInArray(vector<int>& nums) {
        
        int l = 1, r = nums.size() - 1;
        while(l < r){
            int n1 = 0;
            int mid = l + r >> 1;
            for(auto x:nums){
                if(x >= l && x <= mid) n1 ++;
            }
            if(n1 > mid - l + 1) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```

#### Python代码

```python
class Solution(object):
    def duplicateInArray(self, nums):
        """
        :type nums: List[int]
        :rtype int
        """
        l = 1
        r =  len(nums)  - 1
        while l < r:
            mid = l + r >> 1
            s = 0
            for x in nums:
                if x >= l and x <= mid:
                    s += 1
            if s > mid - l + 1:
                r = mid
            else:
                l = mid + 1
        return l
```

### 03. 二维数组中的查找

[原题链接](https://www.acwing.com/problem/content/submission/code_detail/1444364/)

#### 解题思路

1. 蛮力法，直接暴力枚举所有的元素
2. 从右上角开始，如果这个元素小于`target`，则说明当前这一行都不存在，所以向下移动一行，如果大于`target`,说明这一列都不行，向左移动一行。直到找到元素或者出界。之间复杂度为`O(n + m)`。

#### C++代码

```c++
class Solution {
public:
    bool searchArray(vector<vector<int>> array, int target) {
        
        if(array.size() == 0 || array[0].size() == 0) return false;
        int n = array.size(), m = array[0].size();
        int i = 0, j = m - 1;
        while(i < n && j >= 0){
            if(array[i][j] == target) return true;
            else if(array[i][j] < target) i ++;
            else j --;
        }
        return false;
    }
};
```

#### Python代码

```python
class Solution(object):
    def searchArray(self, array, target):
        """
        :type array: List[List[int]]
        :type target: int
        :rtype: bool
        """
        if len(array) == 0 or len(array[0]) == 0:
            return False
        n = len(array)
        m = len(array[0])
        i = 0
        j = m - 1
        while i < n and j >= 0:
            if array[i][j] == target:
                return True
            elif array[i][j] < target:
                i += 1
            else:
                j -= 1
        return False
```

### 04. 替换空格

[原题链接](https://www.acwing.com/problem/content/17/)

语法题，直接做即可

#### C++代码

```c++
class Solution {
public:
    string replaceSpaces(string &str) {
        
        string ans = "";
        for(auto x: str){
            if(x == ' ') ans += "%20";
            else ans += x;
        }
        return ans;
    }
};
```

#### Python代码

```python
class Solution(object):
    def replaceSpaces(self, s):
        """
        :type s: str
        :rtype: str
        """
        return s.replace(" ", "%20")
        
```

### 05. 从尾到头打印链表

[题目链接](https://www.acwing.com/problem/content/description/18/)

#### 解题思路

1. 从头到尾遍历一遍即可，然后再将遍历的结果逆序
2. 先将链表反转，然后在按照顺序输出

#### C++代码

```c++
// 遍历反转
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
 #include<algorithm>
class Solution {
public:
    vector<int> printListReversingly(ListNode* head) {
        vector<int> res;
        while(head){
            res.push_back(head -> val);
            head = head -> next;
        }
        reverse(res.begin(), res.end());
        return res;
    }
};

// 反转链表
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
 #include<algorithm>
class Solution {
public:
    vector<int> printListReversingly(ListNode* head) {
        
        vector<int> res;
        if(!head) return res;
        else{
            ListNode *cur = head, *ne = head -> next;
            head -> next = NULL;
            while(ne){
                ListNode* nne = ne -> next;
                ne -> next = cur;
                cur = ne;
                ne = nne;
            }
            while(cur){
                res.push_back(cur -> val);
                cur = cur -> next;
            }
            return res;
        }
        
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
    def printListReversingly(self, head):
        """
        :type head: ListNode
        :rtype: List[int]
        """
        s = []
        while head:
            s.append(head.val)
            head = head.next
        return s[::-1]
```

### 06. 重建二叉树

[原题链接]()

#### 解题思路

首先按照先序先序遍历的第一个为根，找到其在中序遍历中的位置，然后根据中序遍历的根的位置可以知道左孩子和右孩子分别有几个元素，从而可以确定左孩子和右孩子的先序遍历和中序遍历，然后递归求解即可。

小技巧：

- 由于每次都需要找到根在中序遍历的位置，所以可以先使用一个哈希表将每个元素的位置存下来。

#### C++代码

```C++
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
    vector<int> pre, inor;
    unordered_map<int,int> ma;
    
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        
        pre = preorder;
        inor = inorder;
        for(int i = 0; i < inorder.size(); i ++) ma[inorder[i]] = i;
        TreeNode* root = dfs(0, pre.size() - 1, 0, inor.size() - 1);
        return root;
    }

    TreeNode* dfs(int l1, int r1, int l2, int r2){
        if(l1 > r1) return NULL;
        int idx = ma[pre[l1]];
        auto rt = new TreeNode(pre[l1]);
        rt -> left = dfs(l1 + 1, l1 + idx - l2, l2, idx - 1);
        rt -> right = dfs(l1 + 1 + idx - l2, r1, idx + 1, r2);
        return rt;
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
    def buildTree(self, preorder, inorder):
        """
        :type preorder: List[int]
        :type inorder: List[int]
        :rtype: TreeNode
        """
        if len(preorder) == 0:
            return None
        idx = inorder.index(preorder[0])
        
        rt = TreeNode(preorder[0])
        rt.left = self.buildTree(preorder[1:idx + 1],  inorder[0:idx])
        rt.right = self.buildTree(preorder[idx+1:], inorder[idx+1:])
        return rt
```

### 07. 二叉树的下一个节点

[原题链接](https://www.acwing.com/problem/content/31/)

#### 解题思路

分两种情况讨论：

- 如果当前节点有右子树，那么就是右子树最左侧的那个节点
- 如果当期那节点没有右子树，那么就向上回溯，直到找到一个节点是其父节点的左孩子或者其父节点为空，那么这个节点父节点就是答案。

时间复杂度 `O(h)`，`h`为树高

#### C++代码

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode *father;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL), father(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* inorderSuccessor(TreeNode* p) {
        if(p -> right){
            p = p -> right;
            while(p -> left) p = p -> left;
            return p;
        }
        while(p -> father && p -> father -> right == p) p = p -> father;
        return p -> father;
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
#         self.father = None
class Solution(object):
    def inorderSuccessor(self, q):
        """
        :type q: TreeNode
        :rtype: TreeNode
        """
        if q.right:
            q = q.right
            while q.left:
                q = q.left
            return q
        else:
            while q.father and q.father.right == q:
                q = q.father
            return q.father
```

### 08. 两个栈实现队列

#### 解题思路

一个栈用于存储数据，另一个栈用于转移临时存储。

- 当插入一个元素的时候，直接将其插入到栈顶。
- 当删除一个元素的时候，先将整个栈转移到临时栈中，然后再讲临时栈栈顶元素返回，在将临时栈中的元素压入数据栈中。

#### C++代码

```c++
// 查询和插入次数较多的写法
#include<stack>
class MyQueue {
public:
    /** Initialize your data structure here. */
    stack<int> s;
    MyQueue() {
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        stack<int> t;
        while(s.size()){
            t.push(s.top());
            s.pop();
        }
        s.push(x);
        while(t.size()){
            s.push(t.top());
            t.pop();
        }
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        int t = s.top();
        s.pop();
        return t;
    }
    
    /** Get the front element. */
    int peek() {
        return s.top();
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return s.size() == 0;
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * bool param_4 = obj.empty();
 */

// 插入次数较多的写法
#include<stack>
class MyQueue {
public:
    /** Initialize your data structure here. */
    stack<int> s, cache;
    MyQueue() {
    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        s.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        while(s.size()){
            cache.push(s.top());
            s.pop();
        }
        int res = cache.top(); cache.pop();
        while(cache.size()){
            s.push(cache.top());
            cache.pop();
        }
        return res;
    }
    
    /** Get the front element. */
    int peek() {
       while(s.size()){
           cache.push(s.top());
           s.pop();
       }
       int res = cache.top();
       while(cache.size()){
           s.push(cache.top());
           cache.pop();
       }
       return res;
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return s.size() == 0;
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * bool param_4 = obj.empty();
 */
```

#### Python代码

```python
class MyQueue(object):

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.data1 = []
        self.data2 = []

    def push(self, x):
        """
        Push element x to the back of queue.
        :type x: int
        :rtype: void
        """
        while len(self.data1):
            self.data2.append(self.data1.pop())
        self.data1.append(x)
        while len(self.data2):
            self.data1.append(self.data2.pop())
        

    def pop(self):
        """
        Removes the element from in front of queue and returns that element.
        :rtype: int
        """
        return self.data1.pop()

    def peek(self):
        """
        Get the front element.
        :rtype: int
        """
        return self.data1[-1]

    def empty(self):
        """
        Returns whether the queue is empty.
        :rtype: bool
        """
        return len(self.data1) == 0


# Your MyQueue object will be instantiated and called as such:
# obj = MyQueue()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.peek()
# param_4 = obj.empty()
```

### 09. 斐波拉契数列

[原题链接]()

#### 解题思路

1. 定义法，直接使用递推公式，时间复杂度为`O(n)`
2. 矩阵快速幂法

$$\left[\begin{array}{c} F_n \\ F_{n-1} \end{array} \right] = \left[\begin{array}{cc} 1 & 1\\ 1 & 0 \end{array} \right] \left[\begin{array}{c} F_{n-1} \\ F_{n-2} \end{array}\right]$$

$$\left[\begin{array}{c} F_n \\ F_{n-1} \end{array} \right] = \left[\begin{array}{cc} 1 & 1\\ 1 & 0 \end{array} \right]^{n-1} \left[\begin{array}{c} F_{1} \\ F_{0} \end{array}\right]$$

然后利用快速幂算法，即可在`log n`的时间内得到答案。

#### C++代码

```c++
// 递推法
class Solution {
public:
    int Fibonacci(int n) {
        if(n == 0) return 0;
        if(n == 1) return 1;
        else{
            int f1 = 0, f2 = 1;
            for(int i = 2; i <= n; i ++){
                int f3 = f1 + f2;
                f1 = f2, f2 = f3;
            }
            return f2;
        }
    }
};
```

#### Python代码

```python
class Solution(object):
    def Fibonacci(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n == 0:
            return 0
        elif n == 1:
            return 1
        else:
            a = 1
            b = 0
            for i in range(2, n + 1):
                t = a + b
                b = a
                a = t
            return a
```

### 10. 旋转数组最小数字

[原题链接](https://www.acwing.com/problem/content/20/)

#### 解题思路

如果原始数组中没有重复的元素的话，使用二分是比较简单的，每次取中间的元素，然后通过判断这个元素和区间左侧和右侧的大小关系，可以知道最小的元素是属于左半部分还是右半部分。

但是由于原始数组中存在着重复的元素，那么是不能直接二分的。比如原始数组就只有两类数，不能通过和两个端点进行比较从而得到最小值所处的区间。

可以进行优化。**如果将右边部分区间的后面的和最左侧的相等的部分去除掉**，就可以通过判断大小关系来得到最小值所处的区间。

预处理可能会遍历整个数组，所以时间复杂度可能会退化到`O(n)`

#### C++代码

```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return -1;
        int i = n - 1;
        while(i >= 0 && nums[i] == nums[0]) i --; // 去除右边区间和第一个元素相等的部分
        int l = 0, r = i;
        while(l < r){
            
            int mid = l + r >> 1;
            if(nums[mid] >= nums[l] && nums[mid] > nums[r]) l = mid + 1;  // 如果当前元素大于等于左端点值且大于右端点值，则说明处于左半边上升阶段，否则的话，说明处于右半边上升阶段
            else r = mid;
        }
        return nums[l];
    }
};
```

#### Python代码

```python
class Solution:
    def findMin(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = len(nums)
        if n == 0:
            return -1
        n -= 1;
        while n >= 0 and  nums[n] == nums[0]:
            n -= 1
        l = 0
        r = n
        while l < r:
            mid = l + r >> 1
            if nums[mid] >= nums[l] and nums[mid] > nums[r]:
                l = mid + 1
            else:
                r = mid
        return nums[l]
```

### 11.  矩阵中的路径

[原题链接](https://www.acwing.com/problem/content/21/)

#### 解题思路

经典的DFS加回溯的题目

注意：

- 设置st数组记录每个位置是否被经过，回溯的时候需要清空。

#### C++代码

```c++
class Solution {
public:

    string target;
    int n, m;
    vector<vector<char>> matrix;
    bool st[1000][1000];
    int dirs[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    bool dfs(int a, int b, int cur){
        if(cur == target.size()) return true;
        else{
            st[a][b] = true;
            for(int i = 0; i < 4; i ++){
                int xx = a + dirs[i][0], yy = b + dirs[i][1];
                if(xx >= 0 && xx < n && yy >= 0 && yy < m && !st[xx][yy] && matrix[xx][yy] == target[cur]){
                    bool res = dfs(xx, yy, cur + 1);
                    if (res) return true;
                }
            }
            st[a][b] = false;
            return false;
        }
    }
    bool hasPath(vector<vector<char>>& ma, string &str) {
        if(ma.size() == 0 || ma[0].size() == 0) return false;
        n = ma.size(), m = ma[0].size();
        matrix = ma;
        target = str;
        for(int i = 0; i < n; i ++){
            for(int j = 0; j  <m; j ++){
                if(ma[i][j] == str[0] && dfs(i, j, 1)) return true;
            }
        }
        return false;
    }
};
```

#### Python代码

```python
class Solution(object):
    def hasPath(self, matrix, string):
        """
        :type matrix: List[List[str]]
        :type string: str
        :rtype: bool
        """
        
        self.matrix = matrix
        self.string = string
        if matrix == None or len(matrix) == 0 or len(matrix[0]) == 0:
            return False
        self.n = len(matrix)
        self.m = len(matrix[0])
        self.st = [[False for _ in range(self.m)] for _ in range(self.n)]
        for i in range(self.n):
            for j in range(self.m):
                if matrix[i][j] == string[0] and self.dfs(i, j, 1):
                    return True
        return False
        
    def dfs(self, a, b, cur):
        if cur == len(self.string):
            return True
        self.st[a][b] = True
        dirs = [[-1, 0], [1, 0], [0, -1], [0, 1]]
        for i in range(4):
            xx = a + dirs[i][0]
            yy = b + dirs[i][1]
            if xx >= 0 and xx < self.n and  yy >= 0 and yy < self.m and not self.st[xx][yy] and self.matrix[xx][yy] == self.string[cur]:
                if self.dfs(xx, yy, cur + 1):
                    return True
        self.st[a][b] = False
        return False
```