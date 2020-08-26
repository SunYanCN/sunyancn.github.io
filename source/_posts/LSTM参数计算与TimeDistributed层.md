---
title: LSTM参数计算与TimeDistributed层
categories: 笔记
tags: Keras
abbrlink: 27641
date: 2018-11-14 16:13:03
---
![](https://i.loli.net/2018/11/14/5bebe405d0716.png)
## 前言
本篇主要讲LSTM的参数计算和Keras TimeDistributed层的使用。LSTM的输入格式为：[Simples,Time Steps,Features]
- Samples. One sequence is one sample. A batch is comprised of one or more samples.(就是有几条数据)
- Time Steps. One time step is one point of observation in the sample.（时间步长，通常对应时间序列的长度）
- Features. One feature is one observation at a time step.（一个时间步长对应的向量长度）

举个文本的例子，原始一个batch_size=50,一行文本的长度max_len=10,所以输入矩阵为[50,10],embedding成300维的向量后，格式为[50,10,300]，分别对应Simples,Time Steps,Features。
另外一个需要设置的参数是LSTM的输出维度(Output_dim)，比如为128，LSTM的参数量计算为
$$
\begin{equation}

Number=4*((Features+Output\_dim) x Output\_dim+Output\_dim)

\end{equation}
$$
<!--more-->

## 计算LSTM层的参数

上面计算LSTM参数数目的方法是怎么来的呢？首先理解RNN权重共享。对于LSTM，cell 的**权重是共享**的，这是什么意思呢？这是指图片上三个绿色的大框，即三个 cell ，但是实际上，它只是代表了**一个 cell 在不同时序时候的状态**，所有的数据只会通过一个 cell，然后不断更新它的权重。
我们现在来考虑遗忘门：
![](https://i.loli.net/2018/11/14/5bec0397c15fc.png)
图中的公式 $h_{t-1}$ 的长度是Output_dim， $[h_{t-1},x_{t}]$ 的长度就是Output_dim+Features， $W_{f}$  和 $b_{f}$ 为该层的参数。 $W_{f}$ 的大小为(Output_dim+Features)xOutput_dim,$b_{f}$的大小为Output_dim，所以这个门总的参数为：
$$
\begin{equation}

Number=(Features+Output\_dim) x Output\_dim+Output\_dim

\end{equation}
$$
![1.png](https://i.loli.net/2018/11/14/5bec17fcbdcc4.png)
同样的更新门，有两倍的遗忘门参数。
![2.png](https://i.loli.net/2018/11/14/5bec17fcbc135.png)
更新过程中没有参数需要学习。
![3.png](https://i.loli.net/2018/11/14/5bec17fd1ac9d.png)
输出门参数和遗忘门一样多。
## 一对一序列预测

首先做一个序列的问题，假设有数据X和数据如下，然后用LSTM做序列预测：
```python
X:[ 0.  0.2  0.4  0.6  0.8]
Y:[ 0.  0.2  0.4  0.6  0.8]
```
代码很简单，结果正确：
```python
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import LSTM
from numpy import array
# prepare sequence
length = 5
seq = array([i/float(length) for i in range(length)])
X = seq.reshape(len(seq), 1, 1)
y = seq.reshape(len(seq), 1)
# define LSTM configuration
n_neurons = length
n_batch = length
n_epoch = 1000
# create LSTM
model = Sequential()
model.add(LSTM(n_neurons, input_shape=(1, 1)))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')
print(model.summary())
# train LSTM
model.fit(X, y, epochs=n_epoch, batch_size=n_batch, verbose=2)
# evaluate
result = model.predict(X, batch_size=n_batch, verbose=0)
for value in result:
	print('%.1f' % value)
```
下面我们来手动计算一下LSTM层的参数：
```python
n = 4 * ((inputs + 1) * outputs + outputs^2)
n = 4 * ((1 + 1) * 5 + 5^2)
n = 4 * 35
n = 140
```
全连接层的参数计算如下：
```python
n = inputs * outputs + outputs
n = 5 * 1 + 1
n = 6
```
和Keras打印的参数一致：
```python
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
lstm_1 (LSTM)                (None, 1, 5)              140
_________________________________________________________________
dense_1 (Dense)              (None, 1, 1)              6
=================================================================
Total params: 146.0
Trainable params: 146
Non-trainable params: 0.0
_________________________________________________________________
```
模型的结构图如下：
![](https://i.loli.net/2018/11/14/5bebdfce158d7.png)
## 多对一序列预测
同样准备数据：
```python
X1: 0
X2: 0.2
X3: 0.4
X4: 0.6
X5: 0.8
Y :[ 0.   0.2  0.4  0.6  0.8]
```
代码如下：
```python
from numpy import array
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import LSTM
# prepare sequence
length = 5
seq = array([i/float(length) for i in range(length)])
X = seq.reshape(1, length, 1)
y = seq.reshape(1, length)
# define LSTM configuration
n_neurons = length
n_batch = 1
n_epoch = 500
# create LSTM
model = Sequential()
model.add(LSTM(n_neurons, input_shape=(length, 1)))
model.add(Dense(length))
model.compile(loss='mean_squared_error', optimizer='adam')
print(model.summary())
# train LSTM
model.fit(X, y, epochs=n_epoch, batch_size=n_batch, verbose=2)
# evaluate
result = model.predict(X, batch_size=n_batch, verbose=0)
for value in result[0,:]:
	print('%.1f' % value)
```
用Keras打印网络结构如下：
```python
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
lstm_1 (LSTM)                (None, 5)                 140
_________________________________________________________________
dense_1 (Dense)              (None, 5)                 30
=================================================================
Total params: 170.0
Trainable params: 170
Non-trainable params: 0.0
_________________________________________________________________
```
这一次的参数有170个。原因是全连接层参数变多了,计算如下;
```python
n = inputs * outputs + outputs
n = 5 * 5 + 5
n = 30
```
整体的模型结构如下：
![](https://i.loli.net/2018/11/14/5bebe05eb3187.png)
## 带TimeDistributed的多对多LSTM序列预测
```python
from numpy import array
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import TimeDistributed
from keras.layers import LSTM
# prepare sequence
length = 5
seq = array([i/float(length) for i in range(length)])
X = seq.reshape(1, length, 1)
y = seq.reshape(1, length, 1)
# define LSTM configuration
n_neurons = length
n_batch = 1
n_epoch = 1000
# create LSTM
model = Sequential()
model.add(LSTM(n_neurons, input_shape=(length, 1), return_sequences=True))
model.add(TimeDistributed(Dense(1)))
model.compile(loss='mean_squared_error', optimizer='adam')
print(model.summary())
# train LSTM
model.fit(X, y, epochs=n_epoch, batch_size=n_batch, verbose=2)
# evaluate
result = model.predict(X, batch_size=n_batch, verbose=0)
for value in result[0,:,0]:
	print('%.1f' % value)
```
用Keras打印网络结构如下：
```python
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
lstm_1 (LSTM)                (None, 5, 5)              140
_________________________________________________________________
time_distributed_1 (TimeDist (None, 5, 1)              6
=================================================================
Total params: 146.0
Trainable params: 146
Non-trainable params: 0.0
_________________________________________________________________
```
整体的模型结构如下：
![](https://i.loli.net/2018/11/14/5bebdf475db9f.png)
## Reference
1. [How to Use the TimeDistributed Layer for Long Short-Term Memory Networks in Python](https://machinelearningmastery.com/timedistributed-layer-for-long-short-term-memory-networks-in-python/)
