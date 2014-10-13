title: Python数据结构和模块
date: 2014-10-13 16:28:47
tags: python
categories:
---
##数据结构

###关于列表更多的内容

* list.append(x) => 把一个元素添加到链表的结尾，相当于 a[len(a):] = [x] 。
* list.extend(L) => 将一个给定列表中的所有元素都添加到另一个列表中，相当于 a[len(a):] = L 。
* list.insert(i, x) => 在指定位置插入一个元素。第一个参数是准备插入到其前面的那个元素的索引，例如 a.insert(0, x) 会插入到整个链表之前，而 a.insert(len(a), x) 相当于 a.append(x) 。
* list.remove(x) => 删除链表中值为 x 的第一个元素。如果没有这样的元素，就会返回一个错误。
* list.pop([i]) => 从链表的指定位置删除元素，并将其返回。如果没有指定索引， a.pop() 返回最后一个元素。元素随即从链表中被删除。（方法中 i 两边的方括号表示这个参数是可选的，而不是要求你输入一对方括号，你会经常在 Python 库参考手册中遇到这样的标记。）
* list.index(x) => 返回链表中第一个值为 x 的元素的索引。如果没有匹配的元素就会返回一个错误。
* list.count(x) => 返回 x 在链表中出现的次数。
* list.sort() => 对链表中的元素就地进行排序。
* list.reverse() => 就地倒排链表中的元素。

####把链表当作队列使用

实现队列，使用 collections.deque ，它为在首尾两端快速插入和删除而设计。例如:
```python
>>> from collections import deque
>>> queue = deque(["Eric", "John", "Michael"])
>>> queue.append("Terry")           # Terry arrives
>>> queue.append("Graham")          # Graham arrives
>>> queue.popleft()                 # The first to arrive now leaves
'Eric'
>>> queue.popleft()                 # The second to arrive now leaves
'John'
>>> queue                           # Remaining queue in order of arrival
deque(['Michael', 'Terry', 'Graham'])
```