---
title: 海量数据相似度——局部敏感哈希(LSH)
tags: 局部敏感哈希
abbrlink: 54839
date: 2019-08-10 10:19:13
---
## 场景
**海量高维数据查找与某个数据最相似的一个或者多个数据。**与其它基于Tree的数据结构，诸如KD-Tree、SR-Tree相比，它较好地克服了Curse of Dimension，能够将KNN的时间复杂度缩减到sub-linear。LSH多被用于文本、多媒体（图像、音频）的相似性判断。

<!--more-->

## simhash
谷歌的文档去重算法。主要步骤：
- 对文本进行分词和加权，权重越大，单词重要性越高
- 对单词进行hash：
- 加权：对hash进行加权
- 合并：单词hash相加，得到句子的hash
- 降维：每一位大于0记为1，小于0记为0

[![eLQBes.png](https://s2.ax1x.com/2019/08/10/eLQBes.png)](https://imgchr.com/i/eLQBes)
比较的时候只需要计算两个hash的海明距离：两个二进制串对应的位有几个不一样，那么海明距离就是几，值越小越相似（异或）。

## 局部敏感
[![eLQUSS.png](https://s2.ax1x.com/2019/08/10/eLQUSS.png)](https://imgchr.com/i/eLQUSS)
**存储**：
1、将一个64位的simhash code拆分成4个16位的二进制码。（图上红色的16位）
2、分别拿着4个16位二进制码查找当前对应位置上是否有元素。（放大后的16位）
3、对应位置没有元素，直接追加到链表上；对应位置有则直接追加到链表尾端。（图上的 S1 — SN）

**查找**：
1、将需要比较的simhash code拆分成4个16位的二进制码。
2、分别拿着4个16位二进制码每一个去查找simhash集合对应位置上是否有元素。
2、如果有元素，则把链表拿出来顺序查找比较，直到simhash小于一定大小的值，整个过程完成。

## 与一般Hash的区别
局部敏感hash可以比较相似度，普通的hash不可以

## 参考
- [海量数据相似度计算之simhash和海明距离](http://www.lanceyan.com/tech/arch/simhash_hamming_distance_similarity.html)
- [海量数据相似度计算之simhash短文本查找](http://www.lanceyan.com/tech/arch/simhash_hamming_distance_similarity2-html.html)
- [Locality Sensitive Hash 局部敏感哈希](https://www.bbsmax.com/A/kjdw9a1qJN/)


