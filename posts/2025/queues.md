---
date: "2025-01-21T21:21:00.00Z"
published: true
slug: Queues
tags:
  - Programming
  - coding
  - data structures
  - algorithms
time_to_read: 5
title: Queues
description: Queues are in First in First out data structure
type: post
---

Examples of Queues

1. People standing to be serviced by cashier at a Bank
2. Webserver , requests from clients is serviced in first in , first out manner.

Queue is a two ended data structure

1. Insertion from end
2. Deletion from other end

<table>
<tr>
        <td>front</td>
        <td>.</td>
        <td>.</td>
        <td>.</td>
        <td>.</td>
        <td>.</td>
        <td>back</td>
    </tr>
    <tr>
        <td>10</td>
        <td>5</td>
        <td>1</td>
        <td>4</td>
        <td>12</td>
        <td>15</td>
        <td>21</td>
    </tr>

</table>

```python

def push(self, val):
    self.back+=1
    self.stk[self.back]=val

def pop(self):
    if self.front>self.back:
        return -1
    else:
        if self.back = self.maxSize:
            return
        else:
            self.front+=1

```

Implement queue using stack

```python

class Queue:
    def __init__(self, size:int):
        self.stk , self.stk1 = [], []
        self.front = 0

    def push(self, val:int):
        if self.stk:
            self.front = x
            self.stk.append(x)

    # pop operation is O(n)
    def pop(self):
        while len(self.stk):
            el = self.stk.pop()
            self.stk1.append(el)
        ans = self.stk1.pop()
        while len(self.stk1):
            el = self.stk1.pop()
            self.front = el
            self.stk.append(el)
        return ans

    def peek(self):
        return self.front


```

How can you do the both push and pop operations in O(1)

```python
class Queue:
    def __init__(self):
        self.stk1 , self.stk2 = [], []
        self.front, self.bottomstk1 = 0, 0

    def push(self, x):
        if not (len(self.stk1)) and not len(self.stk2):
            self.front = x
        elif not len(self.stk1):
            self.bottomstk1 = x
        self.stk1.append(x)

    def pop(self):
        if not len(self.stk2):
            while not len(self.stk1):
                self.stk2.append(self.stk1[-1])
                self.stk1.pop()
        ans = self.stk2[-1]
        self.stk2.pop()

        if not self.stk2:
            self.front = self.stk2[-1]
        elif not self.stk1
            self.front = self.bottomstk1
        return ans

    def peek(self):
        return self.front

    def empty(self):
        return len(self.stk1)==0 and len(self.stk2)==0

```

Design stack by Queue

stack : 1 ended
queue : 2 ended

Block on end of queue

```python

class StackByQueue:
    def __init__(self):
        self.queue = Queue()

    def push(self, x):
        self.queue.push(x)

    def pop(self):
       tmp = Queue()
       while len(self.queue):
            tmp.push(self.queue.front())
            self.queue.pop()
        ans = self.queue.front()
        self.queue.pop()
        self.queue =tmp
        return ans

    def top(self):
        return self.queue.back()
    def empty(self):
        return self.queue.empty()

```

Reverse a Queue

```python

def Reverse(queue : Queue):
    if queue.empty():
        return
    el = queue.front()
    queue.pop()
    Reverse(queue)
    queue.push(el)

```

Level Order Traversal

Given a positive number `N` print first `N` natural numbers whose digits
are {1, 2, 3} , print sorted form

N = 10

Output

1, 2, 3, 11, 12, 13, 21, 22, 23, 31

Initialize a queue, and push 0,

1. read the frontend , if not zero , print it
2. push the children
3. pop from front

```python
def printFirst(N:int):
    q = Queue()
    c = 0
    while c<100:
        x = q.front()
        if x!=0:
            print(x)
            c+=1
        q.pop()
        q.push(x*10+1)
        q.push(x*10+2)
        q.push(x*10+3)
```

Binary Forms

Given an integer N>0 , print binary representations of all the numbers that lie between 1 till N

```python

def binaryforms(N:int):
    c = 0
    q = Queue()
    q.push(1)
    while c<N:
        x = q.front()
        q.pop()
        print(x)
        q.push(x*10)
        q.push(x*10 + 1)
        c+=1

        x =
```

Doubly ended Queue

Combination of stack and queue

deque :

1. both insertion and deletion at front and back

Single data structure can be used as stack also

you can push d.push_front(x), and delete d.pop_front()
you can push d.push_back(x), and delete d.pop_back()
d.front(), d.back(), d.size()

**Maximum Sliding Window**

Given an integer array given N elements and also given an integer k, k<=N,

Consider every window/subarray of size k , find maximum in that window

[5, -3, 7, 2, 4, 1, 0]
k = 4

output : [7, 7, 7 ,4]

Use a deque, pop elements from front and push + pop from the back

```python
def maxSlidingWin(arr:list, k:int):
    d = deque()
    n = len(arr)
    ans = []
    for i in range(k):
        insertAtBack(d, i, arr)
    for i in range(k, n, 1):
        ans.push_back(arr[d.front()])
        if d.front() == i-k:
            d.pop_front()
        insertAtBack(d, i, arr)
    ans.push_back(arr[d.front()])
    return ans


def insertAtBack(d:deque, i:int, arr:list):
    while not d.empty() and arr[i]>arr[d.back()]:
        d.pop_back()
    d.push_back(i)

```

Given an integer array given N elements, and integer k , windows of subarray of k
Find first negative integer in every window of size k

```python

def firstNegative(arr:list, k:int):
    n = len(arr)
    q = queue()
    ans = []
    for i in range(k):
        if arr[i]<0:
            q.push(arr[i])

    for i in range(k, n):
        if q.empty():
            ans.append(0)
        else:
            ans.append(arr[q.front()])

        if not q.empty() and q.front()==i-k:
            q.pop()

        if arr[i]<0:
            q.push(i)

    #last window
    if q.empty():
        ans.append(0)
    else:
        ans.append(arr[q.front()])

    return ans

```
