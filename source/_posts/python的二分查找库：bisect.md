---
title: python的二分查找库：bisect
tags: 二分
abbrlink: 9806
date: 2019-08-12 10:53:23
---
具体参考 [文章](http://kuanghy.github.io/2016/06/14/python-bisect)
```python
import bisect

#查找指定区间中包含的元素个数
A = [1,2,2.5,3,3.5,4,5]
lindex = bisect.bisect_left(A,2.5)
rindex = bisect.bisect_right(A,3.5)
print(lindex, rindex, rindex-lindex)

#分数等级
def grade(score,breakpoints=[60, 70, 80, 90], grades='FDCBA'):
    i = bisect.bisect(breakpoints, score)
    return grades[i]

print([grade(score) for score in [33, 99, 77, 70, 89, 90, 100]])

#二分查找
def binary_search_bisect(lst, x):
    from bisect import bisect_left
    i = bisect_left(lst, x)
    if i != len(lst) and lst[i] == x:
        return i
    return None

print(binary_search_bisect(A,4))
```

