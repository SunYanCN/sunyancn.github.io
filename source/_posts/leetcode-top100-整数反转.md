---
title: leetcode top100-整数反转
abbrlink: 51747
date: 2020-02-24 22:41:00
tags:
mathjax:
---
思路:
1. 先判断正负数
2. 将这个数转成字符串再转成字符数组
3. 将数组的首尾元素交换（注意正负）
4. 将字符数组转成字符串再转成整数（注意转换后的数是否超出 int 的取值范围）
5. 将这个整数返回

```python
class Solution:
    def reverse(self, x: int) -> int:
        if x <0:
            res = -int("".join(list(str(-x))[::-1]))
        else:
            res = int("".join(list(str(x))[::-1]))
        
        if -2**31<=res<2**31-1:
            return res
        else:
            return 0
```

