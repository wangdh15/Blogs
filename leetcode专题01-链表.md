---
title: leetcode专题01_链表
date: 2020-05-22 10:15:04
categories:
	- 数据结构与算法
tags:
	- leetcode
	- 链表
---

### 01. 删除链表的倒数第N个节点

[原题链接](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

#### 解题思路

快慢指针法，让一个指针先向前走`N`步，然后加入第二个指针，两个指针同步前进。由于可能要删除第一个节点，所以可以加入头结点，避免特判。

时间复杂度`O(N)`

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *h = new ListNode();
        h -> next = head;
        ListNode* cur = h;
        int cnt = 0;
        while(cnt < n){
            cur = cur -> next;
            cnt ++;
        }
        ListNode* p = h;
        while(cur -> next){
            cur = cur -> next;
            p = p -> next;
        }
        p -> next = p -> next -> next;
        return h -> next;
    }
};
```

### 02. 删除排序链表中的重复元素

[原题链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

#### 解题思路

双指针法，将相同的第一个节点的`next`指针指向下一个不同的节点或者`NULL`

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
    ListNode* deleteDuplicates(ListNode* head) {
        if(!head) return head;
        ListNode* p = head;
        ListNode* q = head -> next;
        while(true){
            while(q && p -> val == q -> val) q = q -> next;
            p -> next = q;
            p = p -> next;
            if(!q) break;
            else q = q -> next;
        }
        return head;
    }
};
```

### 03. 反转链表

[原题链接](https://leetcode-cn.com/problems/reverse-linked-list/)

#### 解题思路

1. 双指针法，一个指向翻转好的头，另一个指向没有反转的尾即可。
2. 递归法，每次传入反转好的头结点的引用，便于下面函数修改，然后递归执行即可。

#### C++代码

```c++
// 迭代法
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
        ListNode *pre = NULL, *cur = head;
        while(cur){
            ListNode *ne = cur -> next;
            cur -> next = pre;
            pre = cur;
            cur = ne;
            
        }
        return pre;
    }
};

// 递归法
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
    
    void f(ListNode* &h, ListNode* p){
        if(! p -> next) {
            h = p;
            return;
        }
        f(h, p -> next);
        p -> next -> next = p;
        return;
    }
    
    ListNode* reverseList(ListNode* head) {
        if(!head) return head;
        ListNode *h;
        f(h, head);
        head -> next = NULL;
        return h;
    }
};
```

### 04.反转链表II

[原题链接](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

#### 解题思路

使用快慢指针法找到对应的区段，然后将区段反转，并和剩余部分拼接起来。

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
    
    void rev(ListNode* begin, ListNode* end){
        
        ListNode* last = NULL;
        while(begin != end){
            ListNode* ne = begin -> next;
            begin -> next = last;
            last = begin;
            begin = ne;
        }
        begin -> next = last;
        return ;
    }
    
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode* h = new ListNode();
        h -> next = head;
        ListNode* p = h;
        int cnt = 0;
        while(cnt < n - m + 1){
            p = p -> next;
            cnt ++;
        }
        ListNode* q = h;
        while(cnt < n){
            p = p -> next;
            q = q -> next;
            cnt ++;
        }
        ListNode *tail = p -> next ;
        // cout << tail -> val << endl;
        rev(q -> next, p);
        // cout << q -> next -> val << endl;
        q -> next -> next = tail;
        q -> next = p;
        return h -> next;
    }
};
```

### 05. 旋转链表

[原题链接](https://leetcode-cn.com/problems/rotate-list/)

#### 解题思路

首先求出链表的总长度，然后对`k`取模(或者直接遍历，遇到结尾就放到头部)，然后用快慢指针法找到对应的分界点，拆分即可。

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
    ListNode* rotateRight(ListNode* head, int k) {
        if(!head) return head;
        int cnt = 1;
        ListNode* p = head;
        while(p -> next){
            cnt ++;
            p = p -> next;
        }
        k %= cnt;
        if(k == 0) return head;
        cnt = 1;
        p = head;
        while(cnt < k + 1){
            p = p -> next;
            cnt ++;
        }
        ListNode* q = head;
        while( p -> next){
            p = p -> next;
            q = q -> next;
        }
        p -> next = head;
        p = q -> next;
        q -> next = NULL;
        return p;
    }
};
```

### 06. 重排链表

[原题链接](https://leetcode-cn.com/problems/reorder-list/)

#### 解题思路

分三步：

1. 快慢指针找到中点
2. 将后半部分反转
3. 合并两个链表

#### C++代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
#include<unordered_map>
class Solution {
public:
    void reorderList(ListNode* head) {
        if(!head || !head -> next) return ;
        ListNode *p = head -> next, *q = head;
        while(p && p -> next){
            p = p -> next -> next;
            q = q -> next;
        }
        ListNode *last = NULL;
        p = q -> next;
        q -> next = NULL;
        while(p){
            ListNode *ne = p -> next;
            p -> next = last;
            last = p;
            p = ne;
        }
        p = last;
        q = head;
        while(p){
            ListNode *t = p -> next;
            p -> next = q -> next;
            q -> next = p;
            q = q -> next -> next;
            p = t;
        }
        return ;
    }
};
```

### 07. 合并两个有序链表

[原题链接](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

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
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(!l1) return l2;
        if(!l2) return l1;
        ListNode *head, *cur;
        if(l1 -> val < l2 -> val) head = l1, l1 = l1 -> next;
        else head = l2, l2 = l2 -> next;
        cur = head;
        while(l1 && l2){
            if(l1 -> val < l2 -> val) cur -> next = l1, l1 = l1 -> next;
            else cur -> next = l2, l2 = l2 -> next;
            cur = cur -> next;
        }
        if(l1) cur -> next = l1;
        if(l2) cur -> next = l2;
        return head;
    }
};
```

### 08. 相交链表

[原题链接]()

#### 解题思路

两个指针，当到达自己的末尾的时候，移到另一个的开头。同时记录是否曾经到过末尾。

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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        bool flagA = false, flagB = false;
        ListNode *p = headA, *q = headB;
        if(!headA || !headB) return NULL;
        while(true){
            if(p == q) return p;
            p = p -> next;
            q = q -> next;
            if(!p){
                if(flagA) return NULL;
                else flagA = true, p = headB;
            }
            if(!q){
                if(flagB) return NULL;
                else flagB = true, q = headA;
            }
        }
    }
};
```

### 09. 环形链表

[原题链接](https://leetcode-cn.com/problems/linked-list-cycle/)

#### 解题思路

快慢指针，有环则必然会相遇。

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
    bool hasCycle(ListNode *head) {
        if(!head) return false;
        ListNode *p = head, *q = head -> next;
        while(p && q){
            if(p == q) return true;
            q = q -> next;
            if(q) q = q -> next;
            else return false;
            p = p -> next;
        }
        return false;
    }
};
```

