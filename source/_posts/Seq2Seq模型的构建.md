---
title: Seq2Seq模型的构建
categories: 项目
tags: Seq2Seq
abbrlink: 33231
date: 2018-10-24 20:24:52
---
![1_1I2tTjCkMHlQ-r73eRn4ZQ.png](https://i.loli.net/2018/10/29/5bd707b5b65d4.png)
Seq2Seq是指一般的序列到序列的转换任务，特点是输入序列和输出序列是不对齐的，比如机器翻译、自动文摘等等。

<!--more-->

假如原句子为X=(a,b,c,d,e,f)，目标输出为Y=(P,Q,R,S,T),则Seq2Seq模型如下：

![seq2seq](https://spaces.ac.cn/usr/uploads/2018/09/3140019013.png)


模型的工作原理如下;

1. Encoder部分首先通过RNN及其变种(LSTM、GRU)等进行编码，讲输入序列编码成一个**定长向量**c，认为这个向量包含了句子的所有信息。得到c有多种方式，最简单的方法就是把Encoder的最后一个隐状态赋值给c，还可以对最后的隐状态做一个变换得到c，也可以对所有的隐状态做变换。

   ![yKJ3iJU.png](https://i.loli.net/2018/10/24/5bcfdae375792.png)

2. Decoder的任务就是把这个**定长向量**进行解码：在给定Target序列的前一个字符，通过训练来预测下一个字符。

![TfGooCw.png](https://i.loli.net/2018/10/24/5bcfdae3d0915.png)

还有一种做法是将c当做每一步的输入：

![3.jpg](https://i.loli.net/2018/10/24/5bcfdb22cda92.jpg)

1. 对于问答系统来说输入包括Questions和Documents两部分，所以要在输入进Decoder的时候要进行融合，可以选择Concatenate。

### Input准备



### Embedding层

```python
from keras.models import Sequential
from keras.layers.embeddings import Embedding

model = Sequential()
model.add(Embedding(input_dim,output_dim, init='uniform', input_length=None, weights=None, W_regularizer=None, W_constraint=None, mask_zero=False))
```

Embedding有一个参数mask_zero,参数的含义是当输入样本的长度不一样时候，首先对数据进行padding补0，然后引入keras的Masking层，它能自动对0值进行过滤。 

![20180509155008234.jpeg](https://i.loli.net/2018/10/22/5bcd7c2573733.jpeg)

比如上面这个 3x4 大小的张量，是经过补零padding的。我希望做axis=1的meanpooling，则第一行应该是 (10+20)/2，第二行应该是 (10+20+30)/3，第三行应该是 (10+20+30+40)/4。这个时候应该是mask_zero=True的，过滤掉0值。

### Dropout

SpatialDropout1D和Dropout的比较。

### Encoder层

```python
keras.layers.recurrent.LSTM(units, activation='tanh', recurrent_activation='hard_sigmoid', use_bias=True, kernel_initializer='glorot_uniform', recurrent_initializer='orthogonal', bias_initializer='zeros', unit_forget_bias=True, kernel_regularizer=None, recurrent_regularizer=None, bias_regularizer=None, activity_regularizer=None, kernel_constraint=None, recurrent_constraint=None, bias_constraint=None, dropout=0.0, recurrent_dropout=0.0)
```

在Keras所有的RNN中，包括simpleRNN, LSTM, GRU等等，输入输出数据格式如下：

![605010.png](https://i.loli.net/2018/10/24/5bd0659510862.png)

例如这样一个数据，总共100条句子，每个句子20个词，每个词都由一个80维的向量表示，输入数据的大小应当是（100, 20, 80）。因此各个维度的含义如下 ：

- samples为样本数目
- timesteps为句子长度（padding后的max_len）
- input_dim为数据的维度

下面的三个代码写法是等价的。

```python
#model.add(LSTM(input_dim=1, output_dim=6,input_length=10, return_sequences=True))
#model.add(LSTM(6, input_dim=1, input_length=10, return_sequences=True))
model.add(LSTM(6, input_shape=(10, 1),return_sequences=True))
```

![20170828113807895.png](https://i.loli.net/2018/10/21/5bcc44890bbdf.png)

return_sequences的含义是每个LSTM单元是否返回输出，我们可以通过上面的图来解释return_sequences：

- return_sequences=True，我们可以获得5个128维的词向量V1’..V5’ 
- return_sequences=False，只输出最后一个红色的词向量

```python
model = Sequential()
model.add(LSTM(128, input_dim=64, input_length=5, return_sequences=True))
```

#### Concatenate

作用是把两个张量在某个维度级联起来。参考下面这个链接。https://nbviewer.jupyter.org/github/anhhh11/DeepLearning/blob/master/Concanate_two_layer_keras.ipynb

#### TimeDistributed

考虑一批32个样本，其中每个样本是一个由16个维度组成的10个向量的序列。该层的批输入形状然后`(32, 10, 16)`。TimeDistributed层的作用就是把Dense层应用到这10个具体的向量上，对每一个向量进行了一个Dense操作，假设是下面这段代码：

```python
model = Sequential()
model.add(TimeDistributed(Dense(8), input_shape=(10, 16)))
```

输出还是10个向量，但是输出的维度由16变成了8，也就是（32,10,8）。

### LSTM模型分析

下图是LSTM的一个典型内部示意图，有三个门：输入门、输出门和遗忘门。

![img](http://file.elecfans.com/web1/M00/63/24/pIYBAFuQetKACYz6AAAv-m33kc8472.png)

|  符号  |          含义          | 符号 |     含义     |
| :----: | :--------------------: | :--: | :----------: |
| C(t-1) | 上一个 LSTM 单元的记忆 | C(t) | 新更新的记忆 |
| h(t-1) | 上一个 LSTM 单元的输出 | h(t) |   当前输出   |
|   σ    |       Sigmoid 层       |  X   |     信息     |
|  tanh  |        tanh 层         |  +   |   增加信息   |


### Attention

![å¸¦Attentionçseq2seq](https://spaces.ac.cn/usr/uploads/2018/09/494964564.png)

Attention的思想是：每一步解码时，不仅仅要结合encoder编码出来的固定大小的向量（通读全文），还要往回查阅原来的每一个字词（精读局部），两者配合来决定当前步的输出。

![L21aLHb.png](https://i.loli.net/2018/10/24/5bcfdb4b02304.png)

### 对话系统

![20170912103504059.jpg](https://i.loli.net/2018/10/21/5bcc76f857700.jpg)

### 训练技巧
1.刚开始内存跑满了，分析了一下原因主要是词典太大，所以对词典进行了词频分析，选出指定大小的常用词，其他低频词语用 `<UNK>` 替换，这样就可以大大减少模型的参数量。
![深度截图_deepin-terminal_20181023130415.png](https://i.loli.net/2018/10/24/5bcfdb856b444.png)

2.采用pickle序列化中间结果，一般来说生成的二进制数据比较大，但是能大大加快读取速度。

3.代码结构函数化，使用面向对象的方式编程，增强代码的可复用性。

4.通过小批量数据验证代码的正确性，方便程序的调试。

5.使用Pycharm远程连接服务器来跑代码，结合计算资源和开发工具，提升开发效率。

### 存在的问题
1.没有使用batch来小批量输入数据。

2.训练和预测使用的decoder结果不同，编写循环的预测decoder。

3.前端的word2vec词向量和最新的ElMo模型的对比实验。

4.对比不同的decoder结构对模型的影响程度。

5.了解Attention原理，在模型中加入Attention来提高准确率。

