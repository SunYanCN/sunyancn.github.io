---
title: 理解Keras LSTM中的参数return_sequences和return_state
categories: 笔记
tags: Keras
abbrlink: 24110
date: 2018-11-14 15:53:57
---
![](https://i.loli.net/2018/11/14/5bebd5524bc47.png)
今天才注意到LSTM的output和hidden_state是同一个东西！下面分情况讨论参数的设置问题。
<!--more-->
### return_sequences=False && return_state=False
```python
h = LSTM(X)
```
Keras API 中，return_sequences和return_state默认就是false。此时只会返回最后一个LSTM的hidden state 值。
### return_sequences=True && return_state=False
输出全部时间步LSTM的hidden state结果。
### return_sequences=False && return_state=True
```python
lstm1, state_h, state_c = LSTM(1, return_state=True)
```
stm1 和 state_h 结果都是 hidden state。在这种参数设定下，它们俩的值相同。都是最后一个时间步的 hidden state。 state_c 是最后一个时间步 cell state结果。
### return_sequences=True && return_state=True
```python
lstm1, state_h, state_c = LSTM(1, return_sequences=True, return_state=True)
```
此时，我们既要输出全部时间步的 hidden state ，又要输出 cell state。lstm1 存放的就是全部时间步的 hidden state。state_h 存放的是最后一个时间步的 hidden state,state_c 存放的是最后一个时间步的 cell state
举一个输出例子，假设我们输入的时间步 time step=3：
```shell
[array([[[-0.02145359],
        [-0.0540871 ],
        [-0.09228823]]], dtype=float32),
 array([[-0.09228823]], dtype=float32),
 array([[-0.19803026]], dtype=float32)]
```
可以看到state_h 的值和lstm1的最后一个时间步的值相同。
### Reference
1. https://blog.csdn.net/u011327333/article/details/78501054
2. [Understand the Difference Between Return Sequences and Return States for LSTMs in Keras](https://machinelearningmastery.com/return-sequences-and-return-states-for-lstms-in-keras/)