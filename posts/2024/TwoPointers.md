---
date: "2024-11-18T21:21:00.00Z"
published: true
slug: TwoPointers
tags:
  - Programming
  - coding
  - data structures
  - algorithms
time_to_read: 5
title: TwoPointers
description: Two pointers is a clever technique of using two variables to solve problems.
type: post
---

Given an integer `Arr[N]` and `sum` , return `True` if $Arr[i]+Arr[j] == sum$ for i=!j

Approach : sort the array and Fix each element and search the element sum-fixed element

Time complexity of this approach : $O(N\log{N})$

```python

def isSum(arr:list, _sum:int):
    arr.sort()
    n = len(arr)
    i = 0
    j = n-1

    while i<j:
        if arr[i]+arr[j] == _sum:
            return True
        elif arr[i]+arr[j]>_sum:
            j-=1
        else:
            i+=1
    return False

arr = [1, 4, 5, 4, 5, 5, 6, 6]
_sum = 6
isSum(arr, _sum)
#True
```

Given the same problem now count of the instances where `a[i]+a[j] == sum`

```python
def isSumCount(arr:list, _sum:int):
    arr.sort()
    n = len(arr)
    i = 0
    j = n-1
    ans = 0
    while i<j:
        if arr[i]+arr[j]>_sum:
            j-=1
        elif arr[i]+arr[j]<_sum:
            i+=1
        else:
            if arr[i] == arr[j]:
                l = (j-i)+1
                ans+=l*(l-1)/2
                return ans

            elei = arr[i]
            elej = arr[j]
            cnti = 0
            cntj = 0
            while i<n and elei==arr[i]:
                cnti+=1
                i+=1
            while j>=0 and elej==arr[j]:
                cntj+=1
                j-=1
            ans+=(cnti * cntj)
    return ans
arr = [1, 4, 4, 5, 5, 5, 6, 6]
_sum = 10
isSumCount(arr, _sum)
#count : 7

```

Given an integer `arr` of size `N` and `diff , Find the count of instances where $arr[i]-arr[j] = diff$

```python

def isDiff(arr:list, diff:int):
    n = len(arr)
    i = 0
    j = 1
    while j<n:
        curr = arr[j]-arr[i]
        if curr<diff:
            j+=1
        elif curr>diff:
            i+=1
            if i==j:
                j+=1
        else:
            return True
    return False
```

```python

def diffCount(arr:list, diff:int):
    ans = 0
    n = len(arr)
    i = 0
    j = 1
    while j<n:
        curr = arr[j]-arr[i]
        if curr<diff:
            j+=1
        elif curr>diff:
            i+=1
            if i==j:
                j+=1
        else:
            c1 , c2 = 0, 0
            p, q = arr[i], arr[j]
            if diff == 0:
                while arr[i]==p:
                    i+=1
                    c1+=1
                    j+=1
                ans += c1*(c1-1)/2
            else:
                while arr[i]==p:
                    i+=1
                    c1+=1
                while arr[j]==q:
                    j+=1
                    c2+=1

                ans+=c1*c2
    return ans

arr = [1, 4, 4, 5, 5, 5, 6, 6]
_diff = 1
isDiff(arr, _diff), diffCount(arr, _diff)

# True, 12

```

Given an integer array , all are nonnegative elements and given a `sum` , if there is a subarray = sum, return True else return False

````python
def isSubSum(arr:list, _sum:int):
    i = 0
    j = 0
    n = len(arr)
    curr = arr[0]
    while j<n:
        if curr<_sum:
            j+=1
            curr+=arr[j] if j<n else 0
        elif curr>_sum:
            curr-=arr[i]
            i+=1
            if i>j:
                j+=1
                if j<n:
                    curr = arr[j]
        else:
            return True

    return False

arr = [5, 3, 4, 6, 2]
_sum = 15

isSubSum(arr, _sum)
#True```
````

Given an integer array , print unique triplets (a, b, c) such that a + b + c = 0

```python

def tripletsum(nums:list):
    n = len(nums)
    for i in range(n):
        if i>0 and nums[i]==nums[i-1]:
            continue
        rem = -1 * nums[i]
        p1 = i+1
        p2 = n-1
        while p1<p2:
            if nums[p1]+nums[p2]<rem:
                p1+=1
            elif nums[p1]+nums[p2]>rem:
                p2-=1
            else:
                print(f"Triplet {nums[i]}, {nums[p1]}, {nums[p2]}")
                if nums[p1] == nums[p2] : break
                else:
                    x, y = nums[p1], nums[p2]
                    while nums[p1]==x:
                        p1+=1
                    while nums[p2]==y:
                        p2-=1

arr = [-5, -5, -3, 0, 1, 1, 12, 3, 3, 4, 4]
tripletsum(arr)
#Triplet : -5, 1, 4
#Triplet : -3, 0, 3

```

Quadruplet sum

```python
def quadrupletSum(nums:list):
    n = len(nums)
    for i in range(n):
        if i>0 and nums[i] == nums[i-1]
        for j in range(1,n):
            if j>i+1 and nums[j] == nums[j-1]:
                continue
            rem = -1*(nums[i]+nums[j])
            p1 = j+1
            p2 = n-1
            while p1<p2:
                if nums[p1]+nums[p2]<rem:
                    p1+=1
                elif nums[p1]+nums[p2]>rem:
                    p2-=1
                else:
                    print(f"Quadruplet {nums[i]}, {nums[j], nums[p1], nums[p2]}")
                    if nums[p1] == nums[p2] : break
                    else:
                        x, y = nums[p1], nums[p2]
                        while nums[p1]==x:
                            p1+=1
                        while nums[p2]==y:
                            p2-=1


arr = [-9, 1, 2, 3, 3, 5, 5, 10]
quadrupletSum(arr)
#Quadruplet -9, (1, 3, 5)
```

Given an sorted integer arr[N] fo distinct elements , Find the No of rectangles l\*b
with distinct config that can be formed using array elements such that area < B

```python
def countRectangles(nums:list, area:int):
    n = len(nums)
    i = 0
    j = n-1
    ans = 0
    while i<=j:
        if nums[i]*nums[j] >= area:
            j-=1
        else:
            ans+= ((j - i + 1) * 2) - 1
            i+=1
    return ans

arr = [2, 3, 5, 6, 7, 9, 20]
countRectangles(arr, 100)
#output : 40
```

Longest substring without repeat

Given a string S, find the length of longest substring with distint characters

```python
def getLongestSub(s:str):
    ans = 1
    n = len(s)
    freq = [0]*27
    i = 0
    j = 0
    freq[ord(s[0])-ord('a')]+=1
    while j < n-1:
        if freq[ord(s[j+1])-ord('a')]==0:
            freq[ord(s[j+1])-ord('a')]+=1
            j+=1
        else:
            freq[ord(s[i])-ord('a')]-=1
            i+=1
        ans = max(ans, j-i+1)
    return ans

st = "abcdefcgklkmpq"
getLongestSub(st)
#output : 7


```

Smallest substring

Given two strings S and T , return min length substring in S that contain all characters in T.

S : ......AXCACPBB ...........
T : BABCA

freqs['B'] >= freqt['B']
freqs['A'] >= freqt['A']
freqs['C'] >= freqt['C']

S: ADOBECODEBANC
T: ABC

Output: BANC

```python


def minSubstring(s:str, t:str):
    if t == "":
        return ""
    freqs = [0]*26
    freqt = [0]*26
    distchars = 0
    for i in range(len(t)):
        if freqt[ord(t[i])-ord('A')] == 0:
            distchars+=1
            freqt[ord(t[i])-ord('A')]+=1
    c ,start, end = 0,0,0
    for i in range(len(s)):
        currchar = ord(s[i])-ord('A')
        freqs[currchar]+=1
        if freqs[currchar] == freqt[currchar]:
            c+=1
        if c == distchars:
            end = i
            break

    if c<distchars:
        return ""

    anss, anse, minm = start, end, end-start+1
    while end < len(s):
        while freqs[ord(s[start])-ord('A')]>freqt[ord(s[start])-ord('A')]:
            freqs[ord(s[start])-ord('A')]-=1
            start+=1
        if end - start + 1<minm:
            minm = end - start +1
            anss= start
            anse = end
        end+=1
        if end<len(s):
            freqs[ord(s[end])-ord('A')]+=1

    return s[anss:anse+1]


s = 'XYABADOBECADEBANC'
t = 'ABC'
minSubstring(s, t)
#output : BECA

```

Given three sorted arrays , A[m], B[n], C[p]

Minimize : max(a, b , c) - min(a, b, c)

Take the three pointers at the starting array and change the pointer which has the smallest value

```python
def minimizeExpression(A:list, B:list, C:list):
    i, j, k = 0, 0, 0
    ans = float("inf")
    m , n , p = len(A), len(B), len(C)
    while i<m and j<n and k<p:
        ans = min(ans, max(A[i], B[j], C[k])-min(A[i], B[j], C[k]))
        minm =min(A[i], B[j],C[k])
        if A[i]==minm:
            i+=1
        elif B[j] == minm:
            j+=1
        else:
            k+=1
    return ans

A = [1, 4, 5,8, 10]
B = [6, 9,15]
C = [2, 3, 6, 6]
minimizeExpression(A, B, C)
#output : 1
```

Given three prime numbers n1, n2, n3 and N, find N-th natural number whose prime factorization contains no other prime factor other than n1/n2/n3
$num = {n1}^i * {n2}^j * {n3}^k$

If num is a valid number then $num * n1$ , $num * n2$ and $num * n3$ will all be valid numbers

<table>
    <tr>
        <td>1</td>
        <td style="color:red">2 --> p5</td>
        <td style="color:green">3 --> p3</td>
        <td>4</td>
        <td style="color:blue">5 -->p2</td>
        <td>6</td>
        <td>8</td>
        <td>?</td>
    <tr>
</table>

P5: Whose 5 multiplier is not there
P3: Whose 3 multiplier is not there
P2: Whose 2 multiplier is not there

Take min of $(5*2, 3*3, 2*5) = 9$

```python
def primefactorNthNumber(n1:int, n2:int, n3:int, N:int):
    arr = [0]*N
    arr[0] = 1
    p1 , p2, p3 = 0, 0, 0
    for i in range(1, N):
        val = min(arr[p1]*n1, arr[p2]*n2, arr[p3]*n3)
        arr[i] = val
        if val == arr[p1]*n1: p1+=1
        if val == arr[p2]*n2: p2+=1
        if val == arr[p3]*n3: p3+=1
    print(arr)
    return arr[N-1]

n1, n2, n3 = 2, 3, 5
N = 10
primefactorNthNumber(2,3, 5, 10)
#output : 12
```
