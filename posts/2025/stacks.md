---
date: "2025-01-08T21:21:00.00Z"
published: true
slug: Stacks
tags:
  - Programming
  - coding
  - data structures
  - algorithms
time_to_read: 5
title: Stacks
description: Stacks are widely used in data structures , especially in recursion.
type: post
---

Stacks are last in , first out data structure.

Examples:

- stack of plates
- recursive call stack

Stack is an one ended data structure

1. push and pop happens from one end.
2. under flow , when you pop when the stack is empty
3. when the stack is full and push makes it a stack overflow

```python

class stack:
    def __init__(self):
        N = 1000
        self.elements = []
        self.top = -1
    def push(self, x):
        if self.isfull():
            return "Stack Overflow"
        else:
            self.elements.append(x)
            self.top+=1
    def pop(self):
        if self.top == -1:
            return "Under Flow"
        else:
            self.elements.pop()
            self.top-=1

    def top(self):
        return self.top

    def isfull(self):
        if self.top == N
```

Reverse a stack , contents in reverse manner

<table>
    <tr>
        <td>a</td>
    </tr>
    <tr>
        <td>b</td>
    </tr>
    <tr>
        <td>c</td>
    </tr>
    <tr>
        <td>d</td>
    </tr>
    <tr>
        <td>e</td>
    </tr>
</table>

1. Use two auxilary arrays , pop elements and push into an auxilary array

2. recursive approach

```python
# Iterative Push Bottom
def pushBottomIterative(stk:stack, x:int):
    tmpstack = stack()
    while stk.empty():
        ele = stk.top()
        tmpstack.push(ele)
        stk.pop()
    stk.push(x)
    while tmpstack.empty():
        ele = tmpstack.top()
        tmpstack.pop()
        stk.push(ele)
    return stk

# Recursive Push Bottom
def pushBottom(stk:stack, x:int):
    if stk.empty():
        stk.push(x)
        return
    top = stk.top()
    stk.pop()
    pushBottom(stk, x)
    stk.push(top)

def reverse(stk:stack):
    if stk.empty():
        return
    y = stk.top()
    stk.pop()
    reverse(stk)
    pushBottom(stk, y)
```

So in this case the first pushbottom will make 1 to the stack , and the second pushbottom , will push 2 to the bottom of the stack and so on.

---

**Next Greater Element**

Given an integer array Arr[N] find the next greater element of every array element

Arr: [4, 5, 2, 25]
NGE: [5, 25, 25, Null]

Brute Force

```python
def getNGE(arr:list):
    n = len(arr)
    NGE = [None]*n
    for i in range(n):
        for j in range(i+1, n):
            if arr[j]>arr[i]:
                NGE[i] = arr[j]
                break
    return NGE
```

Time complexity : (O($N^2$))

```python
def getNGE(arr:list):
    n = len(arr)
    stk = stack()
    stk.push(0)
    nge = [None]*n
    for i in range(1, n):
        while not stk.empty() and arr[i]>stk.top():
            nge[stk.top()] = arr[i]
            stk.pop()
        stk.push(i)
    return nge
```

---

**Largest rectangle**

We are given N histograms

For every bin of the histogram find the next smaller bin and also previous smaller
bin.

```python
def largestRectange(arr:list):
    n = len(arr)
    nse = [None]*n
    pse = [None]*n
    stk = stack()
    stk.push(0)
    for i in range(1, n):
        while not stk.empty() and arr[stk.top()]>arr[i]:
            nse[stk.top()] = i
            stk.pop()
        stk.push(i)
    while not stk.empty():
        nse[stk.top()] = n


    stk.push(n-1)
    for i in range(n-2, -1, -1):
        while not stk.empty() and arr[stk.top()]>arr[i]:
            pse[stk.top()] = i
            stk.top()
        stk.push(i)
    while not stk.empty():
        pse[stk.top()] = -1
        stk.pop()

    maxArea = float("-inf")
    for i in range(n):
        maxArea = max(maxArea, arr[i]*(nse[i]-pse[i]))
    return maxArea

```

---

Given a m \* n Binary matrix , Find the maximum area rectangle whose

1. all cells must contain 1
2. base must be in last row

<table>
        <tr>
            <td>0</td>
            <td>1</td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr>
            <td>1</td>
            <td>0</td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr>
            <td>1</td>
            <td>0</td>
            <td>1</td>
            <td>0</td>
        </tr>
        <tr>
            <td>1</td>
            <td>1</td>
            <td>1</td>
            <td>0</td>
        </tr>
    </table>

Calculate the max 1's starting from bottom of each column , then this problem is similar to
histogram problem.

---

**Change** in the question , same binary matrix m\*n , find max area rectangle such that

1. it contains only ones

Solution : Now one approach is to solve for each row rather than last row and take max of all the individual maxes from each row

```python
    #pseudo code
    for c<-0 to n-1:
        for row <-1 to m-1:
            if A[row][c]!=0:
                A[row][c] = 1+ A[row-1][c]

```

Now for each row just apply histogram problem and get max and answer will be max of maxes

---

Given an integer Arr[N] , find the sum of min element of every subarray.

[2, 1, 5, 9]

smallest element starting from 0 index
2
1
1
1

smallest element starting from 1 index
1
1
1

smallest element starting from 2 index
5
5

smallest element starting from 3 index
9

Brute Force

```python
def minSum(arr:list):
    n = len(arr)
    for i in range(n):
        minm = float("inf")
        for j in range(i, n, 1):
            minm = min(minm, arr[j])
        ans+=minm
    return minm
```

Think reverse lookup, instead of considering all subarrays , can we consider the
contribution of a particular element

```python
def minSum(arr:list):
    n = len(arr)
    nse = [None]*n
    psoe = [None]*n
    st = stack()
    st.push(0)
    for i in range(1, n):
        while not st.empty() and arr[i]<arr[st.top()]:
            nse[st.top()] = i
            st.pop()
        st.push(i)
    while not st.empty():
        nse[st.top()]= n
        st.pop()

    st.push(n-1)
    for j in range(n-2, -1, -1):
        while not st.empty() and arr[i]<=arr[st.top()]:
            psoe[st.top()]=i
            st.pop()
        st.push(i)
    while not st.empty():
        psoe[st.top()]=-1
        st.pop()

    ans = 0
    for i in range(n):
        l1 = (i - psoe[i])
        l2 = (nse[i]-i)
        ans+= l1*l2*arr[i]
    return ans

```

**Remove Duplicates**

Given an strings contains lowercase alpahbets from 'a-z' , remove duplicate letters from the string
such that

1. Every letter comes only once
2. resultant string should be lexicographically smallest

```python

def removeDuplicates(s:str):
    n = len(s)
    freq = [0]*26
    present = [0]*26
    for i in range(n):
        freq[arr[i]-'a']+=1
    st = stack()
    st.push(s[0])
    freq[s[0]]-=1
    present[s[0]] = 1
    for i in range(n):
        freq[s[i]]-=1
        if present[s[i]]:
            continue
        while not st.empty() and s[i]<st.top() and freq[st.top()]>0:
            present[st.top()] = 0
            st.pop()
        st.push(s[i])
        present[s[i]] = i

    # now just reverse the stack
```

**Design Minimum Stack**

```python
class MinStack:
    N = 1000
    def __init__(self):
        self.stk = []
        self.top = -1

    def empty(self):
        if self.stk:
            return True
        else:
            return False

    def push(self, x):
        if self.empty():
            self.stk.append((x, x))
        else:
            minm = min(x, self.top()[1])
            self.stk.append((x, minm))

    def pop(self):
        self.stk.pop()

    def top(self):
        return self.stk.top()[0]

    def getMin(self):
        return self.stk.top()[1]

```

**Efficient min stack**

Whenever there is a change in min , push $2*current_{min} - previous_{min}$

whenever this is a pop $2*current_{min} - st.top()$

```python

class MinStack:
    def __init__(self):
        self.st = stack()
        self.current_min = float("inf")

    def push(self, x):
        if self.st.empty():
            st.push(x)
            self.current_min = x
            return
        else:
            if x < current_min:
                st.push(2*x-self.current_min)
                self.current_min = x
            else:
                st.push(x)
    def pop(self):
        if st.top()<self.current_min:
            self.current_min = 2*self.current_min - self.st.top()
            st.pop()
        else:
            st.pop()

    def top(self):
        if st.top() < self.current_min:
            return self.current_min
        return st.top()

    def getMin(self):
        return self.current_min

```
