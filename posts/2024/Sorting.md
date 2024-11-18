---
date: "2024-10-29T21:21:00.00Z"
published: true
slug: Sorting
tags:
  - Programming
  - coding
  - data structures
  - algorithms
time_to_read: 5
title: Sorting
description: Sorting is a building block of many upstream systems.
type: post
---

The sorted form is nothing a but a specific permutation of the data

<table>
    <tr>
        <td>-5</td>
        <td>7</td>
        <td>1</td>
        <td>-9</td>
    </tr>
</table>

Sorted order in ascending order of value

<table>
    <tr>
        <td>-9</td>
        <td>-5</td>
        <td>1</td>
        <td>7</td>
    </tr>
</table>

Stability means if we have multiple occurences of data , stability means , the occurence order should be maintained in the sorted form.

Why is this important ?

There might be some usecases where we have to sort by order , and order is important

<table>
    <tr>
        <td>7</td>
        <td>-1</td>
        <td>2</td>
        <td>9</td>
        <td>5</td>
    </tr>
</table>

Every language offers an inbuilt sort function , but how does it work ?

Comparision is always done for two items

There are two broad categories of sorting algorithms

1. Comparision based
2. Non comparision based

**Insertion Sort**

<table>
    <tr>
        <td>4</td>
        <td>1</td>
        <td>4</td>
        <td>3</td>
        <td>2</td>
    </tr>
</table>

The word insertion means inserting a new element in its deserving position in an already sorted array

When you pick a element you compare the element with the adjacent element and swap if smaller

What is the default prefix that is already sorted , its the one element is already sorted

<table>
    <tr>
        <td>4</td>
        <td style="color:red">1</td>
        <td>4</td>
        <td>3</td>
        <td>2</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">4</td>
        <td style="color:red">4</td>
        <td>3</td>
        <td>2</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">4</td>
        <td style="color:green">4</td>
        <td style = "color:red">3</td>
        <td>2</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">4</td>
        <td style="color:red">3</td>
        <td style="color:green">4</td>
        <td>2</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">3</td>
        <td style="color:green">4</td>
        <td style ="color:green">4</td>
        <td style = "color:red">2</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">3</td>
        <td style="color:green">4</td>
        <td style ="color:red">2</td>
        <td style="color:green">4</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">3</td>
        <td style="color:red">2</td>
        <td style ="color:green">4</td>
        <td style = "color:green">4</td>
    </tr>
</table>

<table>
    <tr>
        <td style="color:green">1</td>
        <td style="color:green">2</td>
        <td style="color:green">3</td>
        <td style ="color:green">4</td>
        <td style = "color:green">4</td>
    </tr>
</table>

Every element i can atmost be compared with i-1 elements

$1+2+3 ....n-1 = \frac{n(n+1)}{2}$

An array if in descending order the time complexity will be $N^2$

An array is almost ascending order will have the least time complexity

Time Complexity : $O(N^2)$

```python
def insertion_sort(arr:list):
    n = len(arr)
    for i in range(n):
        j=i
        while j>0 and arr[j]<arr[j-1]:
            arr[j], arr[j-1] = arr[j-1], arr[j]
            j-=1
    return arr
arr = [4,1,4,3,2]
insertion_sort(arr)
#output : [1, 2, 3, 4, 4]
```

**Bubble Sort**

Bubbling : It treats the numbers as bubbles. In one round of bubbling all the adjacent bubbles collide with each other , and the larger bubble surpasses the smaller bubble resulting in swapping of smaller bubble with larger bubble on the left side while moving from left to right

In the first round of bubbling the largest element goes to the largest element

In the second round the second largest element goes to the last but one position

<table>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>9</td>
        <td>7</td>
    </tr>
</table>

```python
def bubble_sort(arr:list):
    n = len(arr)
    for i in range(n):
        for j in range(1,n):
            if arr[j-1]>arr[j]:
                arr[j-1], arr[j] = arr[j], arr[j-1]
    return arr
arr = [4,1,4,3,2]
bubble_sort(arr)
#output : [1, 2, 3, 4, 4]
```

```python
#Alternate solution
def bubble_sort(arr:list):
    n = len(arr)
    done = False
    while not done:
        done = True
        i = 0
        while i<n-1:
            if arr[i]>arr[i+1]:
                arr[i], arr[i+1] = arr[i+1], arr[i]
                done =False
            i+=1
    return arr
arr = [4,1,4,3,2]
bubble_sort(arr)
#output : [1, 2, 3, 4, 4]
```

Given an integer array of size `N` find the

1. minimum value of $|A[i] - A[j]|$
2. maximum value of $|A[i] - A[j]|$

where $i!=j$

Brute force : $O(N^2)$

For `2.` approach : $max(arr)-min(arr)$
For `1.` is tricky because the difference can be minimum between any of elements
Find the difference between adjacent elements after sorting and take the minimum of those values

**Manhattan Distance**

In this you can either move `x` axis or `y` axis cant move both

manhattan distance = $|x_1 - x_2| + |y_1 - y_2|$

There are n points `X[N]` and `Y[N]`, find sum of all mnahattan distances among all pairs of points

Brute force : $O(N^2)$

```python
def calculate_manhattan_sum(x_arr:list, y_arr:list):
    n = len(x_arr)
    ans = 0
    for i in range(n):
        for j in range(i, n):
            ans+= abs(x[j]-x[i])+abs(y[j]-y[i])
    return ans

```

Since we have `x's` sum independent of `y's` , we can calculate them individually

First step is to sort them since we will not which points to open from the abs value in negative way

for $x_0$ as the reference point you get

$(x_1-x_0) + (x_2-x_0) + (x_3-x_0) + (x_4-x_0)+ (x_5-x_0) = (x_1+x_2+x_3+x_4+x_5) - 5x_0$

similarly for $x_1$ as reference point you have $(x_2+x_3+x_4+x_5) - 4x_1$

Use suffix sum on the x array

$suffix_{sum}[i+1] - (N-i-1)*x[i]$

You can avoid the suffix sum by just calculating the total sum and then

when you are at `i` index just subtract that index value from the total sum

```python
def calculate_manhattan(x_arr:list, y_arr:list):
    n = len(x_arr)
    totalsum_x = sum(x_arr)
    totalsum_y = sum(y_arr)
    x_arr.sort()
    y_arr.sort()
    ans = 0
    for i in range(n):
        totalsum_x-=x_arr[i]
        totalsum_y-=y_arr[i]
        ans+= (totalsum_x - (n-i-1)*x[i]) + (totalsum_y - (n-i-1)*y[i])
    return ans
```

Given an integer array `arr[N]` , find number of sextuples satisfying the below equation

$\frac{a*b+c}{d} - e = f$

How to find the frequency of an element in an array:

find the first occurence and last occurence of a element

$(a*b)+c = (e+f)*d$

```python

def solveEquation(arr:list):
    n = len(arr)
    LHSArray = []
    RHSArray = []
    for i in range(n):
        for j in range(n):
            for k in range(n):
                LHSArray.append((arr[i]*arr[j])+arr[k])
    for i in range(n):
        for j in range(n):
            for k in range(n):
                if arr[k]:
                    RHSArray.append(arr[i]*(arr[j]+arr[k]))

    RHSArray.sort()
    ans = 0
    def firstOccurence(arr:list, key:int):
        N = len(arr)
        h = N-1
        l = 0
        while l <= h:
            m = (l+h)//2

            if arr[m]>key:
                h = m-1
            elif arr[m]<key:
                l = m+1
            else:
                if m == 0:
                    return m
                elif arr[m-1]!=key:
                    return m
                else:
                    h = m-1
    def lastOccurence(arr:list, key:int):
        N = len(arr)
        h = N-1
        l = 0
        while l <= h:
            m = (l+h)//2

            if arr[m]>key:
                h = m-1
            elif arr[m]<key:
                l = m+1
            else:
                if m == N-1:
                    return m
                elif arr[m+1]!=key:
                    return m
                else:
                    l = m+1

    for element in LHSArray:
        freq = (lastOccurence(RHSArray, element) - firstOccurence(RHSArray, element))
        if freq>0:
            freq+=1
        ans+=freq

    return ans
```

Given an integer array of which all are positive elements and there is another integer k , return the length of smallest subarray with `sum`>=`k`

<table>
    <tr>
    <td>5</td>
    <td>1</td>
    <td>4</td>
    <td>3</td>
    <td>2</td>
    <td>9</td>
    </tr>
</table>

k = 10

In this case the last two elements 2, 9 are of length 2 and make sum = 11> k

```python

def get_smallest_subarray(arr:list, k:int):
    n = len(arr)
    ans = n+1
    for i in range(n):
        _sum=0
        for j in range(i, n):
            _sum+=arr[j]
            if _sum>=k:
                ans = min(ans, j-i+1)
    return ans

arr = [1, 2, 3, 4, 4]
get_smallest_subarray(arr, 5)
#output: 2
```

Think prefix sum and the interesting thing is prefix sum is by default sorted
PS[j]-PS[i-1] from i to j is the subarray sum.

```python
def get_smallest_subarray(arr: list, k: int) -> int:
    n = len(arr)
    prefix_sum = [0] * n
    prefix_sum[0] = arr[0]

    # Build the prefix sum array
    for i in range(1, n):
        prefix_sum[i] = prefix_sum[i - 1] + arr[i]

    ans = n + 1  # Initialize to a value greater than any possible subarray length

    # Loop through each starting point of the subarray
    for i in range(n):
        # We want to find the smallest j such that prefix_sum[j] - prefix_sum[i-1] > k
        # This translates to prefix_sum[j] > k + prefix_sum[i-1]
        target = k + (prefix_sum[i - 1] if i > 0 else 0)

        # Binary search for the smallest j > i
        l, h = i, n - 1
        while l <= h:
            m = (l + h) // 2
            if prefix_sum[m] > target:  # Check if we found a valid subarray
                ans = min(ans, m - i + 1)  # Update the answer
                h = m - 1  # Try to find a smaller j
            else:
                l = m + 1  # We need a larger sum, move to the right

    return ans if ans != n + 1 else 0  # Return 0 if no valid subarray was found

# Example usage
arr = [1, 2, 3, 4, 4]
result = get_smallest_subarray(arr, 5)
print(result)  # Output the result

```

**Inserting range queries**

Given integer `arr` of size `n`, Given `Q` queries each query has $(i_k, j_k)$, we have to add `+1` to all the elements in range of the query, After executing each query , return the final state of the query.

Brute force Time complexity $O(N*Q)$

Consider two queries [2, 5], and [3, 6]

<table>
<tr>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
</tr>
</table>

Query : `[2,5]`

<table>
<tr>
        <td>0</td>
        <td style="color:red">-1</td>
        <td>0</td>
        <td>0</td>
        <td>0</td>
        <td style="color:red">1</td>
        <td>0</td>
        <td>0</td>
</tr>
</table>

Query : `[3,6]`

<table>
<tr>
        <td>0</td>
        <td style="color:red">-1</td>
        <td style = "color:red">-1</td>
        <td>0</td>
        <td>0</td>
        <td style="color:red">1</td>
        <td style="color:red">1</td>
        <td>0</td>
</tr>
</table>

Now if you take `suffix sum`

<table>
<tr>
        <td>0</td>
        <td>0</td>
        <td>1</td>
        <td>2</td>
        <td>2</td>
        <td>2</td>
        <td>1</td>
        <td>0</td>
</tr>
</table>

```python
def range_queries(arr:list, queries:list):
    for i, j in queries:
        arr[j]+=1
        if i>0:
            arr[i-1]-=1

    n = len(arr)
    suffix_sum = [0]*n
    suffix_sum[n-1] = arr[n-1]
    for i in range(n-2, -1, -1):
        suffix_sum[i] = suffix_sum[i+1]+arr[i]
    return suffix_sum

arr = [0]*8
queries= [(2,5),(3, 6)]
range_queries(arr, queries)
#output : [0, 0, 1, 2, 2, 2, 1, 0]

```

**Special Election**

There are N contestants , which have a influence level, supplied in an array.

1. A contestant can cast one vote to other contestants but cant vote himself
2. $i^{th}$ person can vote $j^{th}$ person if $s[i]>=(s[i+1]+s[i+2]....+s[j-1])$

<table>
<tr>
        <td>1</td>
        <td>2</td>
        <td>2</td>
        <td>3</td>
        <td>1</td>
</tr>
</table>

A person can always vote adjacent , since the sum of sandwiched part is zero

1. Fixing the $i^{th}$ person
2. finding out the sandwiched sum for each person

This is $O(N^2)$

`Better approach` : Figure out who all can be voted by $i^{th}$ person

If there is a $j^{th}$ on the right side , first person who cannot be voted , then anything that comes after `j` to `n` person cannot be voted

Are you thinking `"Binary Search"`

So find out for every i , we need to find out [k+1, i-1], [i+1, j-1]
These are nothing but range queries problem

```python

def calculate_votes(S):
    n = len(S)
    prefix_sum = [0] * (n + 1)
    for i in range(n):
        prefix_sum[i + 1] = prefix_sum[i] + S[i]

    votes = [0] * n

    for i in range(n):
        # Binary search for the right range
        low, high = i + 1, n
        while low < high:
            mid = (low + high) // 2
            if prefix_sum[mid] - prefix_sum[i + 1] <= S[i]:
                low = mid + 1
            else:
                high = mid
        right_bound = low - 1

        # Binary search for the left range
        low, high = 0, i
        while low < high:
            mid = (low + high + 1) // 2
            if prefix_sum[i] - prefix_sum[mid] <= S[i]:
                high = mid - 1
            else:
                low = mid
        left_bound = low

        # Apply range update
        if left_bound < i:
            votes[left_bound] += 1
            votes[i] -= 1
        if i < right_bound:
            votes[i + 1] += 1
            if right_bound + 1 < n:
                votes[right_bound + 1] -= 1

    # Calculate the final votes using suffix sum
    for i in range(1, n):
        votes[i] += votes[i - 1]
    return votes

calculate_votes([1, 2, 2, 3, 1])
output: [2, 3, 2, 3 , 1]
```

**Merge Process**

Two arrays in sorted order , how to do combine this two in sorted order in a single array

Array : 1

<table>
    <tr>
        <td>
            2
        <td>
            5
        <td>
            9
        <td>
            11
        <td>
    </tr>
</table>

Array : 2

<table>
    <tr>
        <td>
            7
        <td>
            15
        <td>
            20
        </td>
    </tr>
</table>

output array

<table>
    <tr>
        <td>
            2
        <td>
            5
        <td>
            7
        <td>
            9
     <td>
        11
        <td>
            15
        <td>
            20
        </td>
    </tr>

</table>

Start with smallest array take `i` for tracking the current element of array 1 and `j` for tracking the current element of array 2.

Now using this compare the elements at `i` and `j` and pick element which ever is smaller and increment the index

```python

def merge_sorted_arr(arr1:list, arr2: list):
    m = len(arr1)
    n = len(arr2)
    arr = []

    i = 0; j=0
    while i<m and j<n:
        if arr1[i]<arr2[j]:
            arr.append(arr1[i])
            i+=1
        else:
            arr.append(arr2[j])
            j+=1
    if i<m:
        arr.extend(arr1[i:])
    if j<n:
        arr.extend(arr2[j:])
    return arr

arr1 = [2, 5, 9, 11]
arr2 = [7, 15, 20]
merge_sorted_arr(arr1, arr2)
#output : [2,5,7, 9, 11, 15, 20]

```

**Applying merge process**

Given an sorted integer `Arr[N]` , sort the squares of element of their array

<table>
<tr>
        <td>-4</td>
        <td>-2</td>
        <td>-1</td>
        <td>0</td>
        <td>1</td>
        <td>3</td>
        <td>7</td>
</tr>
</table>

Brute force : Square the elements and do sorting : Time Complexity $O(N)$

Didnt utilized the fact that array is already sorted

Just square and see if there is a pattern

<table>
<tr>
        <td>16</td>
        <td>4</td>
        <td>1</td>
        <td>0</td>
        <td>1</td>
        <td>9</td>
        <td>49</td>
</tr>
</table>

So the observation here is the negative elements squares become descending order
and postive elements are always sorted

So positive elements are `arr1` and negative elements are `arr2`

```python
def sort_squares(arr:list):
    n = len(arr)

    for i in range(n):
        if arr[i]>0:
            break
    index1 = i
    index2 = i-1
    res =[]
    while index1<=n-1 and index2>=0:
        if arr[index1]**2<=arr[index2]**2:
            res.append(arr[index1]**2)
            index1+=1
        else:
            res.append(arr[index2]**2)
            index2-=1
    if index2>=0:
        res.extend([x**2 for x in arr[index2::-1]])
    if index1<n:
        res.extend([x**2 for x in arr[index1:]])
    return res

arr = [-9,-8, -5, -4,-2,-1,	0,	1,	3,	7]

sort_squares(arr)
#output : [0, 1, 1, 4, 9, 16, 25, 49, 64, 81]
```

Little change in the question , we have sorted array , but now we have three integers `A`, `B`, `C`

Now $Arr[i] = A*Arr[i]^2 + B*Arr[i] + C$ and sort it

Now $\frac{df}{dx} = 2Ax+B = 0 => x =\frac{-B}{2A}$ so at this point you get the minimum

If A is positive then parabola is upward facing and if A is negative , the parabola will be downward facing

**Merge Sort**

Merging subarrays of equal sizes , sorted independently

subarray of size `1` by default is already sorted

<table>
<tr>
        <td>8</td>
        <td>7</td>
        <td>6</td>
        <td>5</td>
        <td>4</td>
        <td>3</td>
        <td>2</td>
        <td>1</td>
</tr>
</table>

<table>
<tr>
        <td>7, 8</td>
        <td>5, 6</td>
        <td>3, 4</td>
        <td>1, 2</td>
</tr>
</table>

<table>
<tr>
        <td>5, 6, 7, 8</td>
        <td>1, 2, 3, 4</td>
</tr>
</table>

<table>
<tr>
        <td>1, 2, 3, 4, 5, 6, 7, 8</td>
</tr>
</table>

```python
def main(arr:list):
    def mergesort(arr:list, i:int, j:int):
        if i==j:
            return
        m = (i+j)//2
        mergesort(arr, i, m)
        mergesort(arr, m+1, j)
        merge(arr, i, m, m+1, j)

    def merge(arr:list, s1:int, e1:int, s2:int, e2:int):
        i = s1
        j = s2
        c = s1
        temp = []
        while i<=e1 and j<=e2:
            if arr[i]<arr[j]:
                temp.append(arr[i])
                i+=1
            else:
                temp.append(arr[j])
                j+=1

        if i<=e1:
            temp.extend(arr[i:e1+1])
        if j<=e2:
            temp.extend(arr[j:e2+1])
        arr[s1:e2+1]= temp
        return arr

    mergesort(arr, 0, len(arr)-1)
    return arr

arr = [2, 5, 9, 7, 15, 11, 20]
print("original array:", arr)
print("sorted array : ", main(arr))

# output
# [2, 5 ,9, 7, 15, 11, 20]
# [2, 5 ,7, 9, 11, 15, 20]
```

**Inversion Count**

Given an integer array containing N elements, Find number of pairs (i,j)
$0<=i, j<=N-1 ,  i!=j$ such that:

1. i<j
2. arr[i]>arr[j]

Brute force : write nested for loop and find the indices

```python

for i in range(N):
    for j in range(i+1, N):
        if arr[i]>arr[j]
            cnt+=1

print(cnt)
```

Time Complexity : $O(N^2)$

<table>
<tr>
    <td>5</td>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td>0</td>
    <td>4</td>
</tr>
</table>

output : (5,1),(5,2), (5, 3), (5, 0), (5, 4) (1,0) , (2, 0) , (3, 0)

This count of pairs is called `inversion count`

$I.C(0, N-1) = I.C(0, m) + I.C(m+1, n-1) + I.A.I.C(0..m, m+1 ...n-1)$

How to calculate the inter array inversion count ?

So in the merge process whenever arr[i]>arr[j] , the count the inversions

$cnt+=e1-i+1$

The inversion count is a measure of the `more unsortedness` in the array

```python
def main(arr:list):
    count = 0
    def inversion_count(arr:list, i:int, j:int):
        if i==j:
            return
        m = (i+j)//2
        inversion_count(arr, i, m)
        inversion_count(arr, m+1, j)
        merge(arr, i, m, m+1, j)

    def merge(arr:list, s1:int, e1:int, s2:int, e2:int):
        nonlocal count
        i = s1
        j = s2
        c = s1
        temp = []
        while i<=e1 and j<=e2:
            if arr[i]<arr[j]:
                temp.append(arr[i])
                i+=1
            else:
                temp.append(arr[j])
                count+= e1-i+1
                j+=1

        if i<=e1:
            temp.extend(arr[i:e1+1])
        if j<=e2:
            temp.extend(arr[j:e2+1])
        arr[s1:e2+1]= temp
        return arr

    inversion_count(arr, 0, len(arr)-1)
    return count

arr = [5,1,2,3,0,4]
print("original array:", arr)
print("inversion count : ", main(arr))
#original array: [5, 1, 2, 3, 0, 4]
#inversion count :  8
```

**Applications of Inversion count**

Given an integer array , lets apply insertion sort. Count the number of swaps while doing insertion sort

Given an integer `Arr[n]` find the number of pairs(i,j) such that:

- i<j
- arr[i]>2\*arr[j]

```python
from typing import List
class Solution:
    def reversePairs(self, nums: List[int]) -> int:
        count = 0

        def inversion_count(arr: list, i: int, j: int):
            nonlocal count
            if i >= j:
                return
            m = (i + j) // 2
            inversion_count(arr, i, m)
            inversion_count(arr, m + 1, j)
            inter_inversion_count(arr, i, m, m + 1, j)

        def inter_inversion_count(arr: list, s1: int, e1: int, s2: int, e2: int):
            nonlocal count
            c = s1
            for j in range(s2, e2 + 1):
                while c <= e1 and arr[c] <= 2 * arr[j]:
                    c += 1
                count += e1 - c + 1

            # Now, merge the two sorted halves to maintain the sorted order
            temp = []
            i, j = s1, s2
            while i <= e1 and j <= e2:
                if arr[i] <= arr[j]:
                    temp.append(arr[i])
                    i += 1
                else:
                    temp.append(arr[j])
                    j += 1
            while i <= e1:
                temp.append(arr[i])
                i += 1
            while j <= e2:
                temp.append(arr[j])
                j += 1
            for i in range(len(temp)):
                arr[s1 + i] = temp[i]

        inversion_count(nums, 0, len(nums) - 1)
        return count

Solution().reversePairs([1,3,2,3,1])
# output : 2
```

**Quick Sort**

Given an integer Arr of `N` elements

<table>
<tr>
    <td>4</td>
    <td>7</td>
    <td>1</td>
    <td>2</td>
    <td>9</td>
    <td>3</td>
    <td>10</td>
</tr>
</table>

For example for element `4` if i know the number of elements less than this and number of elements greater than this. Then we will know the position of the element

choose a pivot element , and partition on the left elements smaller than pivot and on right greater than pivot

Worst case of quick sort is $O(N^2)$ since there might be only right problem (which occurs when the array is perfectly sorted)

<table>
<tr>
    <td>40</td>
    <td>10</td>
    <td>15</td>
    <td>90</td>
    <td>14</td>
    <td>80</td>
    <td>7</td>
    <td>96</td>
    <td>95</td>
    <td>12</td>
    <td>76</td>
    <td>87</td>
    <td>99</td>
</tr>
</table>

```python
def main(arr:list):
    def quickSort(i, j):
        if i<j:
            p = partition(i,j)
            quickSort(i, p-1)
            quickSort(p+1, j)

    def partition(i, j):
        left = i+1
        right = j
        while left<=right:
            while left<=j and arr[left]<=arr[i]:
                left+=1
            while right>=i and arr[right]>arr[i]:
                right-=1

            if left<right:
                arr[left], arr[right] = arr[right], arr[left]

        arr[right], arr[i] = arr[i], arr[right]
        return left
    quickSort(0, len(arr)-1)
    return arr

arr = [40,10,15,90,14,80,7,96,95,12,76,87,99]
print("original array:", arr)
print("quick sort array : ", main(arr))
#original array: [40, 10, 15, 90, 14, 80, 7, 96, 95, 12, 76, 87, 99]
#quick sort array :  [7, 10, 12, 14, 15, 40, 80, 76, 87, 90, 95, 96, 99]
```

Given an integer arr of size `N` , push all the 0's to the end

<table>
<tr>
    <td>1</td>
    <td>0</td>
    <td>3</td>
    <td>-4</td>
    <td>2</td>
    <td>0</td>
    <td>-7</td>
    <td>9</td>
</tr>
</table>

```python
def pushZerosRight(arr:list):
    n = len(arr)
    left = 0
    right = n-1

    while left<= right:

        while left<=n-1 and arr[left]!=0:
            left+=1
        while right>=0 and arr[right]==0:
            right-=1

        if left<=right:
            arr[left], arr[right] = arr[right], arr[left]
    return arr

arr = [1, 0, 3, -4, 2, 0, 7, 9]
pushZerosRight(arr)
#[1, 9, 3, -4, 2, 7, 0, 0]

```

Observation : Relative order of non zero numbers will not be maintained here as in the original number.

How can we overcome this ?

```python
def pushZerosOrderedRight(arr:list):
    n = len(arr)
    i = 0
    cnt = 0
    while i<n:
        if arr[i]!=0:
            if i!=cnt:
                arr[i], arr[cnt] = arr[cnt], arr[i]
            cnt+=1
        i+=1
    return arr

arr = [1, 0, 3, -4, 2, 0, 7, 9]
pushZerosOrderedRight(arr)
#[1, 3, -4, 2, 7, 9, 0, 0]
```

<table>
<tr>
    <td>-9</td>
    <td>1</td>
    <td>-7</td>
    <td>-2</td>
    <td>6</td>
    <td>3</td>
    <td>8</td>
    <td>-4</td>
</tr>
</table>

Move all the positive elements to one side and negative elements to the left

```python
def partionNegativePositive(arr:list):
    n = len(arr)
    i = 0
    cnt = 0
    while i<n:
        if arr[i]<0:
            if i!=cnt:
                arr[i], arr[cnt] = arr[cnt], arr[i]
            cnt+=1
        i+=1
    return arr

arr = [9,1,-7,-2,6,3,8,-4]
partionNegativePositive(arr)
#[-7, -2, -4, 1, 6, 3, 8, 9]
```

**Count Sort**

<table>
<tr>
    <td>5</td>
    <td>7</td>
    <td>1</td>
    <td>4</td>
    <td>5</td>
    <td>5</td>
    <td>7</td>
    <td>4</td>
</tr>
</table>

```python
ef countSort(arr:list):
    n = len(arr)
    maxm = max(arr)
    minm = min(arr)
    freq = [0]*(maxm-minm+1)
    for i in range(n):
        freq[arr[i]-minm]+=1

    # creating cumulative frequency array
    for j in range(1, len(freq)):
        freq[j]+= freq[j-1]

    ans = [0]*n
    for ele in arr:
        ans[freq[ele - minm]-1] = ele
        freq[ele - minm]-=1
    return ans
arr = [5,	7,	1,	4,	5,	5,	7,	4]
countSort(arr)
#output : [1, 4, 4, 5, 5, 5, 7, 7]
```

**Radix Sort**

The con of count sort is , if the range of max and min is large in the array , the time complexity will be severely effected

If you want to sort at the level of digits , start from the units digit.

For example

473 , 382

38**2** , 47**3** --> 2 < 3

4**7**3 , 3**8**2 --> 7 < 8

**3**82 , **4**73 --> 3 < 4

<table>
<tr>
    <td>170</td>
    <td>045</td>
    <td>075</td>
    <td>090</td>
    <td>802</td>
    <td>024</td>
    <td>002</td>
    <td>066</td>
</tr>
</table>

We padded extra zeros , to make all digits length equal

Sort based on units digit

<table>
<tr>
    <td>17<b>0</td>
    <td>09<b>0</td>
    <td>80<b>2</td>
    <td>00<b>2</td>
    <td>02<b>4</td>
    <td>04<b>5</td>
    <td>07<b>5</td>
    <td>06<b>6</td>
</tr>
</table>

Sort based on tens digit

<table>
<tr>
    <td>8<b>02</td>
    <td>0<b>02</td>
    <td>0<b>24</td>
    <td>0<b>45</td>
    <td>0<b>66</td>
    <td>1<b>70</td>
    <td>0<b>75</td>
    <td>0<b>90</td>
</tr>
</table>

Sort based on hundreds digit

<table>
<tr>
    <td><b>002</td>
    <td><b>024</td>
    <td><b>066</td>
    <td><b>075</td>
    <td><b>090</td>
    <td><b>024</td>
    <td><b>170</td>
    <td><b>802</td>
</tr>
</table>

The radixSort function orchestrates the sorting process by repeatedly applying counting sort for each digit position, from the least significant digit to the most significant digit.

```python
def countingSort(arr:list, exp:int):
    n = len(arr)
    output = [0]*n
    count  = [0]*10

    for i in range(n):
        index = arr[i] / exp
        count[int(index%10)]+=1

    #cumulative frequency
    for i in range(1, 10):
        count[i]+=count[i-1]

    i = n-1
    while i>=0:
        index = arr[i]/exp
        output[count[int(index%10)]-1]=arr[i]
        count[int(index%10)]-=1
        i-=1

    for i in range(n):
        arr[i]=output[i]

    return arr

def radixSort(arr:list):
    maxm = max(arr)
    exp = 1
    while maxm//exp > 0:
        countingSort(arr, exp)
        exp*=10
    return arr

arr = [ 170, 45, 75, 90, 802, 24, 2, 66]
radixSort(arr)
# [2, 24, 45, 66, 75, 90, 170, 802]
```

**Sorting in Linear Time**

Given an integer elements array where each element in $[0, (N^2 - 1)]$ , Sort almost in linear time

radix sort time complexity : $(N+b)\log_{b}{(max(arr))}$

Applying that in our current problem constraints time complexity of radix sort as we know is $(N+b)\log_{b}{(N^2 -1)}$

Now if we choose our base as $N$ then we can solve the problem in linear time

$N+N \log_{N}{N^2} = 4N = O(N)$
