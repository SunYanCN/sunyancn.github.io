---
title: 'Leetcode 387—字符串中的第一个唯一字符'
tags: 字符串
abbrlink: 56348
date: 2019-08-10 10:05:16
---
![](https://i.loli.net/2019/08/10/3RJKsiQIWL8qfUr.png)
<!--more-->
## 方法一：Hash
和出现次数有关的，不要犹豫，hash
**具体思路**：首先用字典统计每个字符出现的频率，然后顺序遍历字符串，并在字典中进行查找，出现频率为1，返回。
时间复杂度O(n),空间复杂度O(n)
```python
def firstUniqChar(self, s: str) -> int:
        #字典存储字符频率
        dic = dict()
        for i in s:
            if i in dic:
                dic[i] = dic[i] + 1
            else:
                dic[i] = 1
        #顺序查找
        for i in range(len(s)):
            if dic[s[i]] == 1:
                return i
        return -1
```
## 方法二：空间换时间
因为只有26个字母，因此可以开一个26的数组记录每个字符出现的频率。然后顺序查找，频率为1，返回。**优点**：对于很长的字符串节约空间复杂度。
```python
def firstUniqChar(self, s: str) -> int:
    if len(s)==0:
        return -1

    table = [0]*26

    for i in range(len(s)):
        table[ord(s[i])-ord('a')]+=1

    for i in range(len(s)):
        if table[ord(s[i])-ord('a')]==1:
            return i
    return -1
```