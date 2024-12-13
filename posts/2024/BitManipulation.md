---
date: "2024-12-03T21:21:00.00Z"
published: true
slug: BitManipulation
tags:
  - Programming
  - coding
  - data structures
  - algorithms
time_to_read: 5
title: BitManipulation
description: Bit manipulation allows to do operations with speed at the bit level.
type: post
---

Bitwise operators

1. Bitwise `and` b1 & b2 -> 1 only if $b1=b2=1\;$ else 0
2. Bitwise `or` b1 | b2 -> 0 only if $b1=b2=0\;$ else 1
3. Bitwise `xor` b1^b2 --> 1 only if $b1!=b2\;$ else 0
4. Bitwise `left shift`
   - num<<1 for example if 5<<1 --> 101<<1 --> 1010 --> 10
   - num<<2 for example if 5<<2 --> 101<<2 --> 10100 --> 20
   - num<<k for example if 5<<k --> 101<<k --> $5 * 2^k$
5. Bitwise `right shift`
   - num>>1 for example 20>>1 -->10100 --> 1010 --> 10
   - num>>1 for example 20>>2 -->10100 --> 101 --> 5
   - num>>k for example if 20<<k --> 10100<<k --> $20 * 2^{-k}$

**Usage**

Given an integer num , check whether its even or odd

Evenness or oddness of a number is determined by least signifcant bit

for example 5 --> 101 , the `lsb` is 1 hence odd

for example 10 --> 1010 the `lsb` is 0 , hence even

Just take the bitwise and of the number and 1 , then based on the result if its 1 it will be odd else even

```python
def is_odd(num:int):
    '''returns 1 when num is odd and 0 when num is even'''
    return num & 1
```

Given int N , count the number of 1-bits

N = 14 --> 1 1 1 0 --> 3

contraints : 32 bit integer

```python
def count1Bits(num:int):
    mask = 1<<31
    cnt_bits = 0
    while mask:
        cnt_bits+= 1 if mask & num else 0
        mask = mask>>1
    return cnt_bits

```

optimized approach
intuition : consider the number

5 --> 101 ,
now execute 5 & 4 --> 101 & 100 --> 100
now execute 4 and 3 , 100 & 011 --> 0

```python
def countBits(n:int):
    cnt = 0
    while n:
        n= n & (n-1)
        cnt+=1
    return cnt
```

Given two ints left, right , left<=right , find bitwise and of all nums from left to right

l = 8 , r = 11

res = 8 & 9 & 10 & 11

8 | 1 0 0 0

9 | 1 0 0 1

10 | 1 0 1 0

11 | 1 0 1 1
`-----------------`
ans| 1 0 0 0
`-----------------`

The main idea in solving this problem

1. If you consider the msb's of the left and right element given , if they are same then in the whole range that msb wont change
2. If they are same check if its a set bit or not and keep adding the mask to the overall ans
3. The minute msb's are different , all the rest of bits in that range will have a zero somewhere and all will be zero, we handle this in the else case by breaking out of the loop

```python

def rangeBitwiseAnd(m:int, n:int):
    mask = 1<<31
    ans = 0
    while mask:
        if (mask & m) == (mask & n):
            if mask & m:
                ans+=mask
            mask>>=1
        else:
            break
    return ans
```

**Detecing odd one out**

Given an integer element , every element comes twice , just one element comes once , find that one element

        [4, 11, 4, 7, 9, 7 , 11]

```python
    def findOneElement(arr:list):
        ans = arr[0]
        for i in range(1, len(arr)):
            ans = ans ^ arr[i]
        return ans
```

Every element comes even number of times , just 1 element coming for odd number of times

    [5,6,7,5,7,7,7,6,6]

Same Just apply XOR operation

Every element appears thrice , except one element, Find the element ?

    [5, 7, 5, 10, 5, 10, 10]

At the bit level , sum of each ith bit will be divisible by 3 , the modulus between will be 1 , if a single element appears

Steps

1. Sum each ith bit and take modulo by 3

```python

def getSingleElement(arr:list):
    mask = 1<<31
    ans =  0
    for i in range(31,-1, -1):
        cnt = 0
        for j in range(len(arr)):
            if arr[j] & mask:
                cnt+=1
        if cnt%3!=0:
            ans+=mask
        mask>>=1
    return ans
```

**Hamming Distance**

The number of bit positions when they have different bits

a = 4 , b = 17

a : 00100
b : 10001

ouput : 3

Given an integer array given n non negative elements, Find the sum of hamming distances between all pairs

How to solve steps

1. At each bit position calculate the number of ones and number of zeros and multiply those to get the differing count
2. Add this to overall answer.
3. Do 1 and 2 for all bit positions and for all numbers

```python
def hamDist(arr:list):
    n = len(arr)
    ans = 0
    mask = 1<<32
    for i in range(31, -1, -1):
        cnt = 0
        for j in range(n):
            if mask & arr[j]:
                cnt++
        ans += cnt* (n-cnt)
    return ans
```

Given an integer array, Find sum of bitwise-or of all subarrays

arr = [2, 1, 4]

subarrays

[2] -> 2
[2,1] -> 2 | 1
[2, 1, 4] -> 2 | 1 | 4
[1] -> 1
[1, 4] -> 1 | 4
[4] -> 4

Think about contribution due to a particular bit positions
In the bitwise or of how many subarrays , a particular bit $b_i$ is going to be on

consider the binary representation of a set of array elements

<table>
    <tr>
        <td>00101</td>
        <td>01110</td>
        <td>00001</td>
        <td>10110</td>
        <td>01100</td>
        <td>01010</td>
        <td>11100</td>
        <td>01010</td>
    </tr>
</table>

The number of subarrays that start at index = 0 and at $b_4$ bit position , what is the contribution, the nearest $b_4$
bit position to index = 0 which is on is at index = 3 , So all the subarrays that start at index = 0 and pass through index = 3
would contribute to the $b_4$ bit position , this is just $(N-i)*x$ , where x is nothing but ($2^i$) where i is the bit position

Now the logic is to find the next nearest element whose bit is on for a particular bit position , for each element

Why ?

If we find that out , then the number of subarrays that touch that element is n-nearest element where bit position is 1.

```python

def subarray_bitwise_or(arr:list):
    ans = 0
    n = len(arr)
    x = 1<<31
    for i in range(31, -1, -1):
        next = n
        for j in range(n-1, -1, -1):
            if arr[j] & x:
                next = j
            ans+=((n-next)*x)
        x>>=1
    return ans


```

**Finding xor minimum**

Given an ineger array , find the min value of Arr[i] ^ Arr[j] i!=j

Intuition : The min value would be between elements which are similar in their bit positions, so just sort the array , and take xor of adjacent elements and take the minimum of all adjacent xor values

```python

    def xor_minimum(arr:list):
        n = len(arr)
        i, j = 0, 1
        arr.sort()
        ans = float('inf')
        while j < n:
            ans  = min(ans, arr[i] ^ arr[j])
            i+=1
            j+=1
        return ans

```

**XOR Queries**

Given an integer arr of size N, with Q queries, [i, j] , find the bitwise xor of i<=j

[a, b, c, d, e, f]

i = 1 and j = 4

xor(elements from 0 to 4) = a ^ b ^ c ^ d ^ e
xor(elements from 0 to 1) = a ^ b

xor(elements from 1 to 4) = xor (elements from 0 to 4) ^ (xor of elements from 0 to 1) = c ^ d ^ e

Just calculate prefix xors

$Pre_{xor}[j] \^\ Pre_{xor}[i]$

```python
def preXor(arr:list, Q: list):
    n = len(arr)
    pre_xor = [0]*n

    for i in range(1,n):
        pre_xor[i] = (pre_xor[i-1] ^ arr[i])
    ans = []
    for i, j in Q:
        ans.append(pre_xor[j] ^ pre_xor[i-1] if i else pre_xor[j])
    return ans

```

**Pair with given Xor**

Given an integer arr of size N , and given an int K, check if there is a pair (arr[i], arr[j]) such that

arr[i] ^ arr[j] = k

Solution:

arr[i] ^ arr[j] = k

multiply both sides by arr[i] and fix i

arr[j] = arr[i] ^ k, now apply binary search to search this element

**Find two elements**

Given an integer Arr[N] , every element comes twice , just 2 elements come once, find them

[4, 7, 9, 7, 5, 4, 3, 3]

output : 5, 9

Intuition : First take the overall xor , the overall xor is nothing but the xor of two needed elements lets call them n1 and n2

Now find the least set bit which is set (which implies this is the bit position where n1 and n2 differ) , Now categorize

the elements where this position is same for rest of the elements, by doing xor, the final value will be n1 and you can easily get n2 by xoring n1 with overallxor

```python
def findNums(arr:list):
    n = len(arr)
    overallxor = 0

    for i in range(n):
        overallxor ^= arr[i]
    leastsetbit = 1
    while ! (leastsetbit & overallxor):
        leastsetbit<<=1
    int val1 = 0
    for i in range(n):
        if arr[i] & leastsetbit
            val1^=nums[i]
    return val1, overallxor ^ val1

```
