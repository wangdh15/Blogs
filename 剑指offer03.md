---
title: 剑指offer03
date: 2020-05-04 23:02:29
categories:
	- 数据结构与算法
tags:
	- 剑指offer
---

### 23. 反转链表

[原题链接](https://www.acwing.com/problem/content/33/)

#### 解题思路

1. 迭代版直接模拟
2. 递归版需要将结果传入参数进行返回。

#### C++代码

```c++
// 递归版
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

    void f(ListNode* &res, ListNode* p, ListNode* q){
        res = q;
        if(!p){
            return;
        }else{
            ListNode* tt = p -> next;
            p -> next = q;
            q = p;
            p = tt;
            f(res, p, q);
        }
    }
    
    ListNode* reverseList(ListNode* head) {
        ListNode* res;
        if(!head){
            return NULL;
        }
        ListNode* p = head -> next, *q = head;
        q -> next = NULL;
        f(res, p, q);
        return res;
    }
};

// 迭代版
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
    ListNode* reverseList(ListNode* head) {
        ListNode* p = head, *q = NULL;
        while( p ){
            ListNode* pp = p -> next;
            p -> next = q;
            q = p;
            p = pp;
        }
        return q;
    }
};
```

#### Python代码

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        q = None
        while head:
            p = head.next
            head.next = q
            q = head
            head = p
        return q
```

### 24. 合并两个排序的链表

[原题链接](https://www.acwing.com/problem/content/34/)

#### 解题思路

双指针逐个比较

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
    ListNode* merge(ListNode* l1, ListNode* l2) {
        ListNode* head = new ListNode(-1);
        ListNode* p = head;
        while(l1 && l2){
            if(l1 -> val <= l2 -> val){
                p -> next = l1;
                l1 = l1 -> next;
            }else{
                p -> next = l2;
                l2 = l2 -> next;
            }
            p = p -> next;
        }
        if(l1) p -> next = l1;
        if(l2) p -> next = l2;
        return head -> next;
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
    def merge(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        head = ListNode(-1)
        p = head
        while l1 and l2:
            if l1.val <= l2.val:
                p.next = l1
                l1 = l1.next
            else:
                p.next = l2
                l2 = l2.next
            p = p.next
        if l1:
            p.next = l1
        if l2:
            p.next = l2
        return head.next
```

### 25. 树的子结构

[原题链接](https://www.acwing.com/problem/content/35/)

#### 解题思路

遍历A树的每个节点，尝试将当前节点和B树的根节点对齐，B是否是子结构。如果找到，则返回True，否则返回False

`时间复杂度O(nm), n,m分别为两棵树的节点的个数`

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

    bool ok(TreeNode* n1, TreeNode* n2){  // 判断将n1 与 n2 对齐的树是不是为子结构s
        
        if(!n2) return true;
        else{
            if(!n1) return false;
            else{
                if(n1 -> val != n2 -> val) return false;
                else{
                    return ok(n1 -> left, n2 -> left) && ok(n1 -> right, n2 -> right);
                }
            }
        }
        
    }
    
    bool hasSubtree(TreeNode* p1, TreeNode* p2) {
        
        if(!p1 || !p2) return false;
        else // 对齐当前节点，不行的话递归对齐左节点和右节点
            return ok(p1, p2) || hasSubtree(p1 -> left, p2) || hasSubtree(p1 -> right, p2);
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
    
    
    def hasSubtree(self, p1, p2):
        """
        :type pRoot1: TreeNode
        :type pRoot2: TreeNode
        :rtype: bool
        """
        
        def ok(n1, n2):
            if not n2:
                return True
            else:
                if not n1:
                    return False
                else:
                    return n1.val == n2.val and ok(n1.left, n2.left) and ok(n1.right, n2.right)
        
        if not p1 or not p2:
            return False
        else:
            return ok(p1, p2) or self.hasSubtree(p1.left, p2) or self.hasSubtree(p1.right, p2)
            
```

### 26. 二叉树的镜像

[原题链接](https://www.acwing.com/problem/content/37/)

#### 解题思路

交换树的左右孩子，然后递归地将子树镜像。

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
    void mirror(TreeNode* root) {
        if(!root) return ;
        mirror(root -> left);
        mirror(root -> right);
        TreeNode* t = root -> left;
        root -> left = root -> right;
        root -> right = t;
        return ;
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
    def mirror(self, root):
        """
        :type root: TreeNode
        :rtype: void
        """
        if not root:
            return
        root.left, root.right = root.right, root.left
        self.mirror(root.left)
        self.mirror(root.right)
        return root
```

### 27. 对称的二叉树

[原题链接](https://www.acwing.com/problem/content/38/)

#### 解题思路

1. 层序遍历，判断每一层是不是对称的(没有节点的地方也需要加入占位符)
2. 递归。两棵树对称当且仅当一颗树的左子树和另一棵树的右子树是对称的，且两棵树的根节点的val相同。

#### C++代码

```c++
// 递归，两棵树是对称的当且仅当n1的左子树和n2的右子树是对称的，n1的右子树和n2的左子树是对称的。
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
#include<queue>
#include<vector>
class Solution {
public:
    
    bool ok(TreeNode* n1, TreeNode* n2){
        if(!n1 && !n2) return true;
        if(!(n1 && n2)) return false;
        return n1 -> val == n2 -> val &&  ok(n1 -> left, n2 -> right) && ok(n1 -> right, n2 -> left);
    }

    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return ok(root -> left, root -> right);
    }
};

// 层次遍历，判断每一层是不是对称的
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
#include<queue>
#include<vector>
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        queue<TreeNode*> q;
        q.push(root);
        while(q.size()){
            
            queue<TreeNode* > t;
            vector<int> tt;
            while(q.size()){
                auto x = q.front(); q.pop();
                if(x -> left) {
                    tt.push_back(x -> left -> val);
                    t.push(x -> left);
                }
                else {
                    tt.push_back(0x3f3f3f3f);
                }
                if(x -> right){
                    tt.push_back(x -> right -> val);
                    t.push(x -> right);
                }else{
                    tt.push_back(0x3f3f3f3f);
                }
            }
            bool flag = true;
            for(int i = 0; i < tt.size() / 2; i ++){
                if(tt[i] != tt[tt.size() - 1 - i]){
                    flag = false;
                    break;
                }
            }
            if(!flag) return false;
            q = t;
        }
        return true;
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
    def isSymmetric(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        def ok(n1, n2):
            if not n1 and not n2:
                return True
            if not(n1  and n2):
                return False
            else:
                return n1.val == n2.val and ok(n1.left, n2.right) and ok(n1.right, n2.left)
        if not root:
            return True
        else:
            return ok(root.left, root.right)
```

### 28. 顺时针打印矩阵

[原题链接](https://www.acwing.com/problem/content/39/)

#### 解题思路

定义`st`矩阵记录每个格子是否被遍历过。然后定义四个方向。发现朝着当前方向走的话会越界或者到达访问过的节点，则调整自己的方向。

右变下，下变左，左变上，上变右。

#### C++代码

```c++
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        bool st[1000][1000] = {false};
        vector<int> res;
        if(matrix.size() == 0 || matrix[0].size() == 0) return res;
        int i = 0, j = 0, n = matrix.size(), m = matrix[0].size(), cnt = 1;
        char op = 'r';
        res.push_back(matrix[0][0]);
        st[0][0] = true;
        while(cnt < n * m){
            if(op == 'r'){
                if(j + 1 < m && !st[i][j + 1]){
                    j += 1;
                }else{
                    op = 'd';
                    i += 1;
                }
            }else if(op == 'd'){
                if(i + 1 < n && !st[i+1][j]) i ++;
                else op = 'l', j --;
            }else if(op == 'l'){
                if( j - 1 >= 0 && !st[i][j - 1]) j --;
                else op = 'u', i --;
            }else{
                if( i - 1 >= 0 && !st[i - 1][j]) i --;
                else op = 'r', j ++;
            }
            st[i][j] = true;
            res.push_back(matrix[i][j]);
            cnt ++;
        }
        return res;
    }
};

// 代码2,优秀的代码
class Solution {
public:
    vector<int> printMatrix(vector<vector<int> > matrix) {
        bool st[1000][1000];
        vector<int>res;
        if(matrix.size() == 0 || matrix[0].size() == 0) return res;
        int n = matrix.size(), m = matrix[0].size();
        res.push_back(matrix[0][0]);
        st[0][0] = true;
        int dx[4] = {0, 1, 0, -1}, dy[4] = {1, 0, -1, 0};
        int x = 0, y = 0, d = 0;
        for(int i = 1;  i < n * m; i ++){
            int a = x + dx[d], b = y + dy[d];
            if(a < 0 || a >= n || b < 0 || b >= m || st[a][b]){
                d = ( d + 1) % 4;
                a = x + dx[d];
                b = y + dy[d];
            }
            x = a;
            y = b;
            res.push_back(matrix[x][y]);
            st[x][y] = true;
        }
        return res;
        
    }
};
```

#### Python 代码

```python
class Solution(object):
    def printMatrix(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[int]
        """
        dx = [0, 1, 0, -1]
        dy = [1, 0, -1, 0]
        if not matrix or len(matrix) == 0 or len(matrix[0]) == 0:
            return []
        res = []
        n = len(matrix)
        m = len(matrix[0])
        st = [[False for _ in range(m)] for _ in range(n)]
        x = 0
        y = 0
        d = 0
        for i in range(n * m):
            res.append(matrix[x][y])
            st[x][y] = True
            a = x + dx[d]
            b = y + dy[d]
            if a < 0 or a >= n or b < 0 or b >= m or st[a][b]:
                d = ( d + 1) % 4
                a = x + dx[d]
                b = y + dy[d]
            x = a
            y = b
        return res
```

### 29. 包含min函数的栈

[原题链接](https://www.acwing.com/problem/content/90/)

#### 解题思路

维护另一个栈记录当前位置之前的min值

#### C++代码

```c++
#include<stack>
class MinStack {
public:
    /** initialize your data structure here. */
    
    stack<int> data, mi;
    
    MinStack() {
        
    }
    
    void push(int x) {
        data.push(x);
        if(mi.size()) mi.push(min(x, mi.top()));
        else mi.push(x);
    }
    
    void pop() {
        data.pop();
        mi.pop();
    }
    
    int top() {
        return data.top();
    }
    
    int getMin() {
        return mi.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */
```

#### Python代码

```python
class MinStack(object):

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.data = []
        self.mi = []

    def push(self, x):
        """
        :type x: int
        :rtype: void
        """
        self.data.append(x)
        if len(self.mi):
            self.mi.append(min(self.mi[-1], x))
        else:
            self.mi.append(x)
        

    def pop(self):
        """
        :rtype: void
        """
        self.data.pop(-1)
        self.mi.pop(-1)
        

    def top(self):
        """
        :rtype: int
        """
        return self.data[-1]
        

    def getMin(self):
        """
        :rtype: int
        """
        return self.mi[-1]
        


# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(x)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```

### 30. 栈的压入和弹出序列

[原题链接](https://www.acwing.com/problem/content/40/)

#### 解题思路

模拟入栈出栈过程，若当前栈顶元素和出栈的下一个相同，则尝试尽可能多出栈。

否则将元素入栈，栈顶元素和下一个相同。

最终栈为空，且所有元素都入栈且所有出栈元素都得到，则返回True。

#### C++代码

```c++
#include<stack>
class Solution {
public:
    bool isPopOrder(vector<int> pushV,vector<int> popV) {
        
        stack<int> s;
        int i = 0, j = 0;
        while(i < pushV.size() && j < popV.size()){
            while(s.size() == 0 || i < pushV.size() && s.top() != popV[j]){
                s.push(pushV[i]);
                i ++;
            }
            while(s.size() && s.top() == popV[j]){
                s.pop();
                j ++;
            }
        }
        return s.size() == 0 && j == popV.size();
    }
};
```

#### Python代码

```python
class Solution(object):
    def isPopOrder(self, pushV, popV):
        """
        :type pushV: list[int]
        :type popV: list[int]
        :rtype: bool
        """
        s = []
        i = 0
        j = 0
        while i < len(pushV) and j < len(popV):
            while len(s) == 0 or i < len(pushV) and s[-1] != popV[j]:
                s.append(pushV[i])
                i += 1
            while len(s)  and j < len(popV) and s[-1] == popV[j]:
                s.pop(-1)
                j += 1
        return len(s) == 0 and j == len(popV)
```

### 31. 不分行从上往下打印二叉树

[原题链接](https://www.acwing.com/problem/content/41/)

#### 解题思路

二叉树的层序遍历

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
#include<queue>
class Solution {
public:
    vector<int> printFromTopToBottom(TreeNode* root) {
        vector<int> res;
        if(!root) return res;
        queue<TreeNode*> q;
        q.push(root);
        while(q.size()){
            auto it = q.front(); q.pop();
            res.push_back(it -> val);
            if(it -> left) q.push(it -> left);
            if(it -> right) q.push(it -> right);
        }
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
class Solution:
    def printFromTopToBottom(self, root):
        """
        :type root: TreeNode
        :rtype: List[int]
        """
        if not root:
            return []
        s = [root]
        res = []
        while s:
            it = s.pop(0)
            res.append(it.val)
            if it.left:
                s.append(it.left)
            if it.right:
                s.append(it.right)
        return res
```

### 32. 分行从上往下打印二叉树

[原题链接](https://www.acwing.com/problem/content/42/)

#### 解题思路

1. 层序遍历，每一层的节点放在一个队列中
2. 在每一层的结尾插入一个`NULL`标记

#### C++代码

```c++
// 分层遍历
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
#include<queue>
class Solution {
public:
    vector<vector<int>> printFromTopToBottom(TreeNode* root) {
        vector<vector<int>> res;
        if(!root) return res;
        queue<TreeNode*> q;
        q.push(root);
        while(q.size()){
            queue<TreeNode*> t;
            vector<int> tt;
            while(q.size()){
                auto it = q.front(); q.pop();
                tt.push_back(it -> val);
                if(it -> left) t.push(it -> left);
                if(it -> right) t.push(it -> right);
            }
            res.push_back(tt);
            q = t;
        }
        return res;
    }
};

// 在每次层后面插入一个标记
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
#include<queue>
class Solution {
public:
    vector<vector<int>> printFromTopToBottom(TreeNode* root) {
        vector<vector<int>> res;
        if(!root) return res;
        queue<TreeNode*> q;
        q.push(root);
        q.push(NULL);
        vector<int> cur;
        while(q.size()){
            auto it = q.front();
            q.pop();
            if(it){
                cur.push_back(it -> val);
                if(it -> left) q.push(it -> left);
                if(it -> right) q.push(it -> right);
            }else{
                res.push_back(cur);
                cur.clear();
                if(q.size()) q.push(NULL);  // 当队列不空才可以加入NULL，否则最后一行会陷入死循环，无限加入NULL
            }
        }
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
    def printFromTopToBottom(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        s = [root, None]
        res = []
        if not root:
            return []
        t = []
        while s:
            it = s.pop(0)
            if it:
                t.append(it.val)
                if it.left:
                    s.append(it.left)
                if it.right:
                    s.append(it.right)
            else:
                if s:
                    s.append(None)
                res.append(t[:])
                t = []
        return res
```

### 33. 之字形打印二叉树

[原题链接](https://www.acwing.com/problem/content/43/)

#### 解题思路

设置一个flag变量标志着当前是奇数行还是偶数行即可。

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
#include<queue>
#include<algorithm>
class Solution {
public:
    vector<vector<int>> printFromTopToBottom(TreeNode* root) {
        bool flag = true;
        vector<vector<int>> res;
        if(!root) return res;
        queue<TreeNode*> q;
        q.push(root);
        q.push(NULL);
        vector<int> t;
        while(q.size()){
            auto it = q.front(); q.pop();
            if(it){
                t.push_back(it -> val);
                if(it -> left) q.push(it -> left);
                if(it -> right) q.push(it -> right);
            }else{
                if(q.size()) q.push(NULL);
                if(!flag){
                    reverse(t.begin(), t.end());
                    flag = true;
                }else{
                    flag = false;
                }
                res.push_back(t);
                t.clear();
            }
        }
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
    def printFromTopToBottom(self, root):
        """
        :type root: TreeNode
        :rtype: List[List[int]]
        """
        res = []
        if not root:
            return res
        q = [root, None]
        t = []
        flag = True
        while q:
            it = q.pop(0)
            if it:
                t.append(it.val)
                if it.left:
                    q.append(it.left)
                if it.right:
                    q.append(it.right)
            else:
                if q:
                    q.append(None)
                if not flag:
                    t = t[::-1]
                    flag = True
                else:
                    flag = False
                res.append(t)
                t = []
        return res
```

