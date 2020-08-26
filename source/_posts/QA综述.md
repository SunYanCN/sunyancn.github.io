---
title: QA综述
categories: 项目
tags:
  - NLP
  - QA
abbrlink: 6322
date: 2018-12-03 21:11:30
---
## Memory Networks

### 论文简介

1. [Memory Networks](http://arxiv.org/pdf/1410.3916v11.pdf) ，**ICLR 2015**，Facebook AI Research
2. [Answering Reading Comprehension Using Memory Networks](http://cs224d.stanford.edu/reports/KapashiDarshan.pdf)，Stanford

(2018年12月3日补充：第二篇不是论文，应该是斯坦福写的一个类似教程之类的东西，但是写的太像论文了(ˇˍˇ)，我都搞混了 )

看名字就知道第一篇是原论文，第二篇是第一篇的实现。（这里必须吐槽一下，第一篇论文一个图都没有，让我这种看论文先看图的人瞬间丧失好感。第二篇就人性化的多，简单的一个模型架构图就能瞬间让人领悟许多。）

- **起因**：作者认为RNN、Attention机制把很长的语句编码为定长向量(Context Vector)作为模型的记忆，会带来很大的信息压缩损失
- **想法**:   既然是因为向量太小带来的问题，那就直接加一个外部记忆模块，称之为Memory
- **评价**：开山之作，但是模型比较简单，也没有特别具体的说明。



在第二篇论文里面的引言部分有一些比较精辟的话，我也摘录出来，不妨作为NLP的部分真理以供参考。

1. QA是最容易评估AI的标准。(这个容易理解，图灵测试不就是这样吗？)
2. AI要想很好的完成QA任务，要在两个方面得到提高：检索和推理。
3. 单纯的记忆是不够的，他们需要一些知识去检索。(人不也经常使用浏览器吗？)

<!--more-->

### 具体实现

看下面这个图就很清楚，这个模型主要包括五个部分：Memory单元和I、G、O、R。

- Input模块：输入的文本经过Input模块编码成特征向量(各种方式都可以，最简单就是wordvec)。
- Generalization模块：根据特征向量对Memory单元进行读写操作，即更新记忆。
- Output模块：根据Question（也会进过Input模块进行编码）对Memory单元进行组合（比如加权），得到编码的输出向量
- Response模块：根据输出向量编码生成一个自然语言的答案出来。



![](https://i.loli.net/2018/12/03/5c050e6481611.png)



举个例子。假设有下面一段文字，针对文字可以提出几个问题，然后让模型去回答：

![](https://i.loli.net/2018/12/03/5c05183c3962f.png)

这里我们首先使用Input模块对文档进行编码，Generalization模块存储编码结果，然后使用Input对问题where is the milk now？进行编码，Output模块根据问题编码后的向量从Memory单元中选出最相关的一句话：Joe left the milk，然后再对剩下的记忆进行评分，找出与where is the milk now？和Joe left the milk最相关的memory。我们发现是Joe travelled to the office。这样我们就找到了最相关的记忆，接下来使用R模块对所有的单词进行评分找到得分最高的单词作为答案即可。

### 代码复现

1. [Theano版本的代码](https://github.com/pararthshah/qa-memnn/blob/master/memnn_theano_v3.py)



## End-To-End Memory Networks

### 论文简介

- [End-To-End Memory Networks](https://arxiv.org/pdf/1503.08895v5.pdf)，Facebook AI Research

这是Facebook AI在Memory networks后的续作。上文记忆网络介绍模型并非端到端的QA训练，该论文End-To-End Memory Networks就在上文的基础上进行端到端的模型构建,减少生成答案时需要事实依据的监督项，在实际应用中应用意义更大。

**End to end**：一端输入我的原始数据，一端输出我想得到的结果。只关心输入和输出，中间的步骤全部都不管。

### 具体实现

![](https://i.loli.net/2018/12/03/5c051a6d2d277.png)

这一篇文章提到一个更加具体但是复杂一点的模型如上图所示。

模型主要的参数包括A,B,C,W四个矩阵，其中A,B,C三个矩阵就是embedding矩阵，主要是将输入文本和Question编码成词向量，W是最终的输出矩阵。从上图可以看出，对于输入的句子s分别会使用A和C进行编码得到Input和Output的记忆模块，Input用来跟Question编码得到的向量相乘得到每句话跟q的相关性，Output则与该相关性进行加权求和得到输出向量。然后再加上q并传入最终的输出层。

#### 输入输出模块

通过把每句话压缩成一个向量对应到memory中的一个slot（上图中的蓝色或者黄色竖条），将输入的文本转化成向量并保存在memory中(通过词向量得到句向量)。论文中提出了两种编码方式，BoW和位置编码：

- BoW：直接把词向量加起来，会丢失位置关系。
- 位置编码：认为不同位置的单词的权重是不一样的，然后对各个单词的词向量按照不同位置权重进行加权求和得到句子表示。

输入模块把输入文本编码为向量，保存在Input和Output两个模块中，Input模块用于跟Question相互作用得到各个memory slot与问题的相关程度，使用Output模块的信息产生输出。

- Input模块：将Question经过输入模块编码成一个向量u，与$m_{i}$维度相同，然后将其与每个$m_{i}$点积得到两个向量的相似度，在通过一个softmax函数进行归一化得到$p_{i}$，$p_{i}$就是q与$m_{i}$的相关性指标。
- Output模块：对其中各个记忆ci按照pi进行加权求和即可得到模型的输出向量o。

#### Response模块

Response模块主要是根据输出向量o和问题向量q产生最终的答案。其结合o和q两个向量的和与W相乘在经过一个softmax函数产生各个单词是答案的概率，值最高的单词就是答案。并且使用交叉熵损失函数最为目标函数进行训练。

#### 多层模型

将多个单层模型进行stack在一块，结构图如下所示：

![](https://i.loli.net/2018/12/03/5c052a23169f2.png)

上面几层的输入就是下层o和u的和。至于各层的参数选择，论文中提出了两种方法（主要是为了减少参数量，如果每层参数都不同的话会导致参数很多难以训练）。

- **Adjacent**：这种方法让相邻层之间的A=C。也就是说$A{k}+1=C_{k}$，此外W等于顶层的C，B等于底层的A，这样就减少了一半的参数量。
- **Layer-wise（RNN-like)**：与RNN相似，采用完全共享参数的方法，即各层之间参数均相等。由于这样会大大的减少参数量导致模型效果变差，所以提出一种改进方法，即令$u_{k+1}=u_{k}+o_{k}$，也就是在每一层之间加一个线性映射矩阵H。

#### 论文公式

具体的公式如下：

![](https://i.loli.net/2018/12/03/5c051dd1571df.png)

### 代码实现

1. [End-To-End Memory Network using Tensorflow](https://github.com/domluna/memn2n)



## Dynamic Memory Networks

### 论文简介

- [Ask Me Anything: Dynamic Memory Networks for Natural Language Processing](https://arxiv.org/pdf/1506.07285v3.pdf)

论文中说NLP中很多任务都可以归结为QA问题，所以本文的DMN模型以QA为基础进行训练，但是可以扩展到很多别的任务中，包括序列标注、分类、翻译等等。

### 具体实现

![](https://i.loli.net/2018/12/03/5c0531196f1d0.png)