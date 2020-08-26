---
title: leetcode top 100 1—two sum
tags: leetcode
mathjax: true
abbrlink: 47969
date: 2020-02-24 22:04:42
---
这道题本身如果通过暴力遍历的话也是很容易解决的，时间复杂度在 O(n2)，由于哈希查找的时间复杂度为 O(1)，所以可以利用哈希容器map降低时间复杂度(这里使用python的字典)。
### 方法1：两重循环
会报超时错误
```python
def two_sum(nums, target):
    for i, n in enumerate(nums):
        for j, m in enumerate(nums):
            if target - n == m and i!=j:
                return [i,j]
```
时间复杂度：O(n2)
空间复杂度：O(n)
### 方法2：两遍Hash
```python
def two_sum(nums, target):
    dct = dict(zip(nums, range(len(nums))))
    for i, n in enumerate(nums) and i!=dct[target - n]:
        if target - n in dct:
            return [dct[target - n], i]
```
时间复杂度：O(n2)
空间复杂度：O(n)
### 方法2：一遍Hash(边循环边查字典)
```python
def two_sum(nums, target):
    dct = {}
    for i, n in enumerate(nums):
        if target - n in dct:
            return [dct[target - n], i]
        dct[n] = i
```
时间复杂度：O(n2)
空间复杂度：O(n)