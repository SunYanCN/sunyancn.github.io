---
title: '轻量终端打印工具:wasabi'
categories: 工具
tags: wasabi
abbrlink: 23212
date: 2018-11-29 21:21:09
---
![](https://i.loli.net/2018/11/29/5bffea4e6ae22.png)
可以用于终端打印一些信息
<!--more-->
## 安装
```shell
pip install wasabi
```
## 代码以及对应的执行效果
```python
from wasabi import Printer

msg = Printer()
msg.text("Hello world!")

msg.good("Success")
msg.fail("Error")
msg.warn("Warning")
msg.info("Info")

msg.divider("Heading",char='-')

import time
with msg.loading("Loading..."):
    # Do something here that takes longer
    time.sleep(3)
msg.good("Successfully loaded something!")

from wasabi import color

formatted = color("This is a text", fg="white", bg="green", bold=True)
print(formatted)

from wasabi import wrap

wrapped = wrap("Hello world, this is a text.", indent=2)
print(wrapped)
```
![](https://i.loli.net/2018/11/29/5bffea4e6ae22.png)
