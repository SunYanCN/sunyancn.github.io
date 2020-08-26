---
title: 最近调代码两个的bug
tags: bug
mathjax: false
abbrlink: 19186
date: 2019-11-06 16:01:21
---
### 标签错误
错误log:
```bash
RuntimeError: cuda runtime error (59) :device-side assert triggered
```
一般是标签出错，检查两点：
- 标签中是否有-1
- 标签个数和分类的个数是否匹配（检查模型最后的分类个数）

### Shell脚本dos2unix
```bash
Shell脚本出现$'\r': command not found
```
这是因为脚本文件可能在window弄过，有window下的空行，把他转换成unix格式的就行。
```bash
yum install dos2unix
dos2unix run.sh
```
