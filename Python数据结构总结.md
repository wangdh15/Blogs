---
title: Python数据结构总结
date: 2020-04-13 15:14:48
categories:
	- 数据结构与算法
tags:
	- Python
---

主要是总结各种Python内置的数据结构的API，方便刷题使用

### 堆和优先队列

#### 利用heapq

heapq内部是维护列表实现的。提供了一下堆的常用操作。常用的API如下所示：

```python
import heapq
# heapq实现堆是最小堆，没有最大堆
# 根据已有的list创建一个最小堆
a = [2,1,5,-10,6]
heapq.heapify(a)  # 从a构造堆 线性时间，自底向上构造堆

# 向队列中增加一个元素
heapq.heappush(a, -100)

# 弹出队列中的最小值
heapq.heappop(a)
# 如果只是想访问队列中的最小值，直接访问a[0]即可

# 增加一个元素并弹出最小值
heapq.heappushpop(a, -5)

# 弹出一个元素并增加一个元素
heapq.heapreplace(a, 10)

# 合并两个有序的迭代器，返回一个新的迭代器
heapq.merge([-1, -2, 10, 100], [-100, 0, 5])
# 可以自己按照接口制定元素比较的关键字
heapq.merge(*iterables, key=None, reverse=False)

# 返回可迭代对象的前n个最大值
heapq.nlargest(n, iterable, key=None)
heapq.nlargest(2, [1,2,3,4,5,6,7])
heapq.nlargest(2, [1,2,3,4,5,6,7], key=lambda x:-x)  # 返回前两个最小值

# 返回可迭代对象的前n个最小值
nsmallest(n, iterable, key=None)
heapq.nsmallest(3, [2, -10, 5, 5])

# 支持元祖类型的数据，比较按照从前到后
h = []
heappush(h, (5, 'write code'))
heappush(h, (7, 'release product'))
heappush(h, (1, 'write spec'))
heappush(h, (3, 'create tests'))
heappush(h, (3, 'add tests'))
heappop(h)
```

为了让heapq支持自定义的类型，可以重写自定义类的`__lt__`方法

在heapq模块内部，元素的上浮和下沉的比较就是通过对象的<比较实现，以下是源码

```python
def _siftdown(heap, startpos, pos):
    newitem = heap[pos]
    # Follow the path to the root, moving parents down until finding a place
    # newitem fits.
    while pos > startpos:
        parentpos = (pos - 1) >> 1
        parent = heap[parentpos]
        if newitem < parent:
            heap[pos] = parent
            pos = parentpos
            continue
        break
    heap[pos] = newitem
```

下面是自己定义类，支持`heapq`的操作

```python
import heapq
class A:
  def __init__(self, a, b):
    self.a = a
    self.b = b
  
  def __lt__(self, other):  # 实现类的__lt__方法，也可以实现__ge__, __le__, __cmp__等方法
    if self.b < other.b:
      return True
    elif self.b == other.b and self.a < other.a:
      return True
    else:
      return False
  
  def __repr__(self):
    return "a={}, b={}".format(self.a, self.b)
  

h = []
heapq.heappush(h, A(1, 2))
heapq.heappush(h, A(1, 3))
heapq.heappush(h, A(2, 3))
heapq.heappush(h, A(2, 10))
heapq.heappush(h, A(1, 10))
while len(h):
  print(heapq.heappop(h))
```

#### 优先级队列(PriorityQueue)

优先级队列内部就使用的是`heapq`实现的，同时是线程安全的。API和`Queue`是相同的。

```python
import queue

q = queue.PriorityQueue()
q.put()  # 添加
q.get() # 获取
q.empty() # 判断是否为空
q.size() # 大小
```

同样支持自定义的类型，需要实现一些魔法方法。**优先级队列实现的也是最小堆**

上述的`heapq`和`PriorityQueue`都只支持最小堆，如果需要使用最大堆的话，可以在`push`的时候将key变为原来的负数，

然后在`pop`出来之后再将其取负即可。

