---
title: nohup详解
tags: nohup
mathjax: false
abbrlink: 48347
date: 2019-11-02 14:13:49
---
功能：使程序在后台运行，当连接服务器终端时，程序还在运行
### 后台运行的命令
```python
nohup python 文件名.py &
# 会出现一个进程后，记住这是该程序的进程号，
# 后期如果想要停止，可以使用该命令：kill -9 进程号
```
### 查看后台运行的进程
```python
# 如果发现没记nohup运行的进程，可以使用以下的命令来查看进程号
1. jobs -l # 查看后台运行的进程，如果后台运行的进程很多，可以使用下面的命令
2. ps aux | grep nohup 查看nohup 的进程，然后使用 kill -9 进程号杀死该进程
```
### nohup的输出重定向问题
如果后台同时运行多个程序，它们的输出全都在nohup.out中，阅读起来很不方便。可通过重定向到不同文件中来解决这个问题;也可以通过在不同的文件下，运行nohup来避免这种情况。
linux启动后，会默认打开3个文件描述符，0表示标准输入，1表示正确输出，2表示错误输出。
```python
nohup python 文件名.py 1>result.out 2>result.out & 
# 将正确输出和错误输出均写入result.out文件
```
### 查看运行输出
```python
#实时查看
tailf result.out
#查看几行
head 10 result.out #查看文件的前面10行
tail 10 result.out # 查看文件的后面10行
```