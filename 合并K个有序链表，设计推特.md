---
title: 合并K个有序链表，设计推特
date: 2020-04-13 16:42:52
categories:
	- 数据结构与算法 
tags:
	- leetcode
	- 堆
---

### 1. 合并K个有序链表

[原题链接](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

#### 思路1 逐个比较，堆优化

借鉴合并两个有序链表的思想，可以使用k个指针，每个指针指向一个链表，然后每步比较所有链表中

值，找到最小的那一个，添加到结果中即可。

时间复杂度`O(NK)`， 空间复杂度`O(1)`

但是能不能比较高效地得到一堆元素中的较小值？考虑到每次都是增加一个元素，然后去最小值，删除一个元素。所以可以使用堆来优化。这样就可以每次在`O(log K)`的时间内得到最小值。

时间复杂度为`O(N logK)`， 空间复杂度`O(K)`

#### python 代码

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

from queue import PriorityQueue as pq

class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        
        q = pq()
        p = lists
        h = ListNode(0)
        cur = h
        for i, l in enumerate(lists):
            if l:
                q.put((l.val, i))   # 由于ListNode节点没有实现__lt__方法，所以这里存储其属于的链表index，同时维护lists里面对应的指针
        while not q.empty():
            _, i = q.get()
            cur.next = lists[i]
            cur = cur.next
            lists[i] = lists[i].next
            if lists[i]:
                q.put((lists[i].val, i))
        return h.next
```

#### 思路2 分治的思想

还是参考两个链表合并的过程，可以先将链表分为两组，然后分别合并，最终再将两个有序链表合并。

时间复杂度`O(N log K)` (每次合并需要N， 一共需要log K 次双链表合并)。

空间复杂度`O(1)`。合并两个有序链表只需要常数的空间复杂度。

#### python 代码

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        
        def f(n1, n2):  # 合并两个有序链表的算法
            head = ListNode(0)
            cur = head
            while n1 and n2:
                if n1.val < n2.val:
                    cur.next = n1
                    n1 = n1.next
                else:
                    cur.next = n2
                    n2 = n2.next
                cur = cur.next
            if n1:
                cur.next = n1
            if n2:
                cur.next = n2
            return head.next
        
        if len(lists) == 0:  # 边界情况处理
            return None
        if len(lists) == 1:
            return lists[0]
        mid = len(lists) >> 1
        n1 = self.mergeKLists(lists[:mid])
        n2 = self.mergeKLists(lists[mid:])
        return f(n1, n2)
```

### 2. 设计推特

[原题链接](https://leetcode-cn.com/problems/design-twitter/submissions/)

#### 解题思路

主要的难点是如何获取推特。按照上面的处理思路，将每个人发的推特按照双向链表存储起来，然后合并K个有序链表即可

#### python代码

```python
from queue import PriorityQueue as pq

class ListNode:

    def __init__(self, ID=None, Time=None):
        self.id = ID
        self.time = Time
        self.next = None
        self.prev = None

    def __lt__(self, other):
        return self.time < other.time

class User:

    def __init__(self):
        self.follows = set()  # 存储自己follow的用户的id
        self.tweets = ListNode()  # 存储自己发送的tweet的id,用链表存储
        self.tail = ListNode()
        self.tweets.next = self.tail
        self.tail.prev = self.tweets
        self.size = 0

    def post(self, id, time):
        t = ListNode(id, time)
        t.next = self.tweets.next
        t.prev = self.tweets
        t.next.prev = t  # 在当前人的链表头处加入推文
        t.prev.next = t
        self.size += 1
        if self.size > 10:  # 超出10条推文就将之前的删除
            self.tail.prev.prev.next = self.tail
            self.tail.prev = self.tail.prev.prev
            self.size -= 1


class Twitter:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.users = {}  # 用户id和用户类之间的映射
        self.time = 0  # 表示每个tweet发出的时间,每发一个减一

    def postTweet(self, userId: int, tweetId: int) -> None:
        """
        Compose a new tweet.
        """
        if userId not in self.users:
            self.users[userId] = User()
        self.users[userId].post(tweetId, self.time)  # 将tweetID存放到对应用户的信息中
        self.time -= 1

    def getNewsFeed(self, userId: int):
        """
        Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent.
        """
        q = pq()
        if userId not in self.users:
            return []
        res = []
        lists = []
        lists.append(self.users[userId].tweets.next)
        for x in self.users[userId].follows:
            lists.append(self.users[x].tweets.next)
        for x in lists:
            if x.id:
                q.put((x.time, x))
        while len(res) < 10 and not q.empty():
            _, x = q.get()
            res.append(x.id)
            if x.next.id:
                q.put((x.next.time, x.next))

        return res

    def follow(self, followerId: int, followeeId: int) -> None:
        """
        Follower follows a followee. If the operation is invalid, it should be a no-op.
        """
        if followerId == followeeId:  ## 自己不能follow自己
            return
        if followerId not in self.users:
            self.users[followerId] = User()
        if followeeId not in self.users:
            self.users[followeeId] = User()
        self.users[followerId].follows.add(followeeId)

    def unfollow(self, followerId: int, followeeId: int) -> None:
        """
        Follower unfollows a followee. If the operation is invalid, it should be a no-op.
        """
        if followerId in self.users and followeeId in self.users and followeeId in self.users[followerId].follows:
            self.users[followerId].follows.remove(followeeId)  # 删除,如果两个Id不是都在users中，那么就不做操作
```

