---
title: 'Attention,BiLSTM以及Transform'
categories: 笔记
tags: NLP
abbrlink: 50119
date: 2018-11-06 09:58:37
---
## Attention
{% dplayer "url=https://jalammar.github.io/images/attention_process.mp4"
"https://i.loli.net/2018/11/06/5be0f69d922e7.png" 
"api=https://api.prprpr.me/dplayer/"  "loop=yes" 
"theme=#FADFA3" 
"autoplay=false" 
"token=tokendemo"  %}

<!--more-->

上面这个视频很好的总结了Attention的各个步骤：

1. 首先收集(储存)Encoder的每一步的隐藏状态的输出向量。
2. 对每个隐藏状态进行评分（先不考虑如何评分）。
3. 对评分进行Softmax概率化。
4. 把得到的概率与对应的输出向量相乘，放大具有高分数的隐藏状态，淹没低分数的隐藏状态。
5. 对加权后的向量求和，得到输出。

{% dplayer "url=https://jalammar.github.io/images/attention_tensor_dance.mp4"
"https://i.loli.net/2018/11/06/5be10280edc01.png" 
"api=https://api.prprpr.me/dplayer/"  "loop=yes" 
"theme=#FADFA3" 
"autoplay=false" 
"token=tokendemo"  %}

在具体的每一个时间单元步里面执行过程如下：

1. Decoder RNN接受'<END>'的词向量和Decoder的初始向量。
2. 经过RNN处理，产生输出和隐藏向量(h4),丢掉输出。
3. Attention:使用储存的Encoder词向量和h4向量来计算时间步的上下文向量(C4)。
4. h4和C4进行拼接得到这一个RNN的输出。
5. 通过Dense+softmax来得到字典中每个字的概率，从而最大化输出字标签。
6. 这一时间步执行完毕，把h4传递到下一个RNN,下一个RNN的输入为上一个RNN的输出。

把每一个时间步骤汇总起来就得到了最后的输入输出的Attention矩阵：
![](https://i.loli.net/2018/11/06/5be102811cbdf.png)
上面的过程搞明白后，现在的问题就是怎么对几个向量进行评分。

## Transformer （Attention Is All You Need）
正如论文的题目所说的，Transformer中抛弃了传统的CNN和RNN，整个网络结构完全是由Attention机制组成。更准确地讲，Transformer由且仅由self-Attenion和Feed Forward Neural Network组成。一个基于Transformer的可训练的神经网络可以通过堆叠Transformer的形式进行搭建，作者的实验是通过搭建编码器和解码器各6层，总共12层的Encoder-Decoder，并在机器翻译中取得了BLEU值得新高。
![](/Attention,BiLSTM以及Transform/20181106111716656.png)
作者采用Attention机制的原因是考虑到RNN（或者LSTM，GRU等）的计算限制为是顺序的，也就是说RNN相关算法只能从左向右依次计算或者从右向左依次计算，这种机制带来了两个问题：

1. 时间片 t 的计算依赖 t-1 时刻的计算结果，这样限制了模型的并行能力；
2. 顺序计算的过程中信息会丢失，尽管LSTM等门机制的结构一定程度上缓解了长期依赖的问题，但是对于特别长期的依赖现象,LSTM依旧无能为力。