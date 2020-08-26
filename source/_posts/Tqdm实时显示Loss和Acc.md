---
title: Tqdm实时显示Loss和Acc
tags: Tqdm
mathjax: false
abbrlink: 61076
date: 2019-11-02 14:12:26
---
### 代码示例
```python
from random import random
from time import sleep
from tqdm import tqdm

epochs = 2
train_data_num = 10
for i in range(epochs):
    with tqdm(total=train_data_num) as t:
        for j in range(10):
            # Description will be displayed on the left
            t.set_description('Epoch %i' % i)
            # Postfix will be displayed on the right,
            # formatted automatically based on argument's datatype
            t.set_postfix(loss=random(),acc=random())
            sleep(0.1)
            t.update(1)
```
输出结果：
```python
Epoch 0: 100%|██████████| 10/10 [00:01<00:00,  9.94it/s, acc=0.0863, loss=0.999]
Epoch 1: 100%|██████████| 10/10 [00:01<00:00,  9.93it/s, acc=0.459, loss=0.921]
```