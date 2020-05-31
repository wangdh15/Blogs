---
title: LRU、LFU缓存机制
date: 2020-04-05 22:01:59
categories:
	- 数据结构与算法
tags:
	- 系统设计
	- leetcode
---

## 1. LRU机制实现

[原题链接](https://leetcode-cn.com/problems/lru-cache/)

### 解题思路

散列表和双向链表联合使用

使用散列表可以在**`O(1)`**的时间利用**`key`**找到对应的节点，双向链表维护的是节点的访问次序，当访问了一个节点

将其在双向链表中删除，并添加到链表头或者链表尾中， 容量满之后只需要删除链表头或者链表尾的元素即可

时间复杂度**`O(1)`**， 空间复杂度**`O(n)`**

### 代码

```python
class LinkNode:

    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.next = None
        self.prev = None
        self.ok = True


class LRUCache:

    def __init__(self, capacity: int):
        '''
        使用字典来记录数据是否存在，字典中存储的值是节点，完成O(1)时间的查询
        利用双向链表来组织数据，完成O(1)时间的插入和删除
        '''
        self.record = {}
        self.size = 0
        self.capacity = capacity
        self.head = LinkNode(None, None)
        self.tail = LinkNode(None, None)
        self.head.next = self.tail
        self.tail.prev = self.head        
    

    def _add_head(self, node:LinkNode) -> None:
        '''
        将一个节点插入到头结点之后的位置
        '''
        node.prev = self.head
        node.next = self.head.next
        self.head.next = node
        node.next.prev = node
    
    def _delete_node(self, node:LinkNode) -> None:
        '''
        删除一个节点
        '''
        node.prev.next = node.next
        node.prev.next.prev = node.prev

    def get(self, key: int) -> int:
        '''
        获取一个元素
        '''
        item = self.record.get(key, None)
        if item and item.ok:
            self._delete_node(item)
            self._add_head(item)
            return item.value
        else:
            return -1
            
    def put(self, key: int, value: int) -> None:
        '''
        新增数据
        '''
        item = self.record.get(key, None)
        if item:
            item.value = value
            self.get(key)
        else:
            temp = LinkNode(key, value)
            self.record[key] = temp
            if self.size == self.capacity:
                temp1 = self.tail.prev
                del self.record[temp1.key]
                self._delete_node(temp1)
                self._add_head(temp)
            else:
                self._add_head(temp)
                self.size += 1
```

## 2. LFU缓存机制

[原题链接](https://leetcode-cn.com/problems/lfu-cache/)

### 解题思路

对每一个freq构造一个双向列表，用两个hash表，一个用于直接访问到点，另一个用于访问每个freq对应的节点

同时维护一个min_freq,用于删除元素。修改min_freq只可能有两种情况:

- 访问了一个key，这个key的freq为min_freq对应的freq加了1 原来所在的链表为空了，这个时候需要将min_freq+1

- 新加了一个key，这个时候min_freq为1

时间复杂度`O(1)`, 空间复杂度为`O(n)`

### 代码

```python
class Node:
    """
    双向链表节点类
    """
    def __init__(self, key = None, value = None, freq = 0):
        self.key = key
        self.value = value
        self.prev = None
        self.next = None
        self.freq = freq
        
class LinkList:
    """
    双向链表类
    """
    def __init__(self):
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head
        
    def add_from_head(self, node):
        """
        向链表头添加一个节点
        """
        node.next = self.head.next
        node.prev = self.head
        node.prev.next = node
        node.next.prev = node
    
    def delete_from_tail(self):
        """
        删除链表尾部的一个节点
        """
        t = self.tail.prev
        self.delete(self.tail.prev)
        return t
        
    def delete(self, node):
        """
        将某个节点从其所在的链表中删除
        """
        node.prev.next = node.next
        node.next.prev = node.prev
        

class LFUCache:

    def __init__(self, capacity: int):
        self.d = {}
        self.freq = {}
        self.min_freq = 0
        self.cap = capacity
        self.freq[1] = LinkList()
        
    def update(self, node):
        """
        访问完一个节点之后对这个节点更新
        增加这个节点的freq，维护min_freq
        """
        self.freq[node.freq].delete(node)   #  将节点从之前的链表中删除
        node.freq += 1
        if node.freq not in self.freq:      # 将节点添加到新的链表中
            self.freq[node.freq] = LinkList()
        self.freq[node.freq].add_from_head(node)
        if self.freq[self.min_freq].head.next == self.freq[self.min_freq].tail:  # 维护min_freq
            self.min_freq += 1
        
    def get(self, key: int) -> int:
        """
        获取某个key对应的value
        """
        if key in self.d:
            self.update(self.d[key])
            return self.d[key].value
        else:
            return -1
        
    def put(self, key: int, value: int) -> None:
        """
        添加某个元素
        """
        if self.cap == 0:
            return 
        
        if key in self.d:
            self.d[key].value = value
            self.update(self.d[key])
        else:
            if len(self.d) == self.cap:
                t = self.freq[self.min_freq].delete_from_tail()
                del self.d[t.key]
            a = Node(key, value, 1)
            self.freq[1].add_from_head(a)
            self.min_freq = 1
            self.d[key] = a
# Your LFUCache object will be instantiated and called as such:
# obj = LFUCache(capacity)
# param_1 = obj.get(key)
# obj.put(key,value)
```
