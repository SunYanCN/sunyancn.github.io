---
title: NLP面试比较重要的知识点
tags: 面试
abbrlink: 21849
date: 2019-07-22 19:27:10
---

![ei2rqS.png](https://s2.ax1x.com/2019/07/22/ei2rqS.png)
<!--more-->
## 基础
Python内存管理的方式  [参考答案](http://kkpattern.github.io/2015/06/20/python-memory-optimization-zh.html)
## 机器学习
1. 逻辑回归完整推导过程
2. 逻辑回归属于线性分类器还是非线性分类器？(同属于广义线性模型)
3. 朴素贝叶斯为什么朴素？（假设特征之间相互独立）
4. 介绍一下SVM核函数
5. MLP和MAP的区别（MAP加入了先验信息）
6. svm如何处理多分类（一对多法设计k个分类器，一对一法设计k(k-1)/2个分类器）
7. svm对缺失数据敏感吗，为什么，决策树呢。（svm没有处理缺失数据的方法，决策树有）
8. 为什么svm采用最大间隔（最大间隔得到决策边界是唯一的，具有鲁棒性）
9. 聚类了解哪些？基于密度的聚类了解哪些？（**原型聚类**：KMeans,高斯混合聚类，**密度聚类**：DBSCAN、Mean-Shift，**层次聚类**：AGNES、BIRCH，**谱聚类**）

## 深度学习基础
1. L1、L2正则（贝叶斯角度分析，加入了先验信息）
2. 怎样判断过拟合？过拟合怎么解决？（1.**交叉验证**的结果比单次的结果下，2.通过**学习曲线**Learning Curves判断，3.实际的话也可以通过**观察**训练误差很小，而预测误差很大的模型通常过拟合）
3. 梯度消失和梯度爆炸的原因，为什么rnn比cnn和全连接层神经网络更容易发生梯度消失或爆炸（RNN在梯度的反向传播中只有连乘，LSTM和GRU加入了门机制，梯度的计算中同时还有$h_t$的加法）
4. 说一下dropout的训练和测试过程（在训练过程中以一定的概率p的使神经元失活,测试过程不需要dropout，对层的输出乘以p）
5. 说一下BN和LN，有什么区别，BN为什么可以提升效果()
6. CNN的旋转不变性怎么理解(CNN具有平移不变性)

## 自然语言处理基础
输入补全可以用哪个数据结构来做？（字典树）
假如有10亿条搜索请求，怎么找出最热的前10条？
讲一下LDA，讲一下隐狄利克雷分布，里面有个辛普森采样了解吗
pointwise、pairwise 、listwise的区别
word2vec是有监督的还是无监督的
word2vec的损失函数形式
分层softmax和负采样原理
Glove的思想以及和word2vec的区别
Fasttext和word2vec的区别
Fasttext哈希规则，怎么把语义相近的词哈希到一个桶里
RNN、LSTM、GRU公式。
RNN、LSTM、GRU参数大小
Attention机制的原理，有哪些变种
sigmoid用作激活函数时，分类为什么要用交叉熵损失，而不用均方损失？


## 文本分类
精确率，召回率，F1值
![](https://i.loli.net/2019/07/22/5d35bda25c13e20464.png)
AUC和ROC
![](https://i.loli.net/2019/07/22/5d35beceefac074363.png)

```bash
Precision：P=TP/(TP+FP)
Recall：R=TP/(TP+FN)
F1-score：2/(1/P+1/R)
ROC/AUC：TPR=TP/(TP+FN), FPR=FP/(FP+TN)
```
讲一下Textcnn的过程
Fasttext怎么做分类的
CNN和LSTM都可以用于分类，两者用于分类有什么区别？
样本不均衡的处理方法


### Sequence Label
有了BILSTM为什么还要用CRF？
BILSTM+CRF的训练目标？状态转移矩阵是joint learn的吗？维度是多少？
维特比算法的时间复杂度
HMM和CRF的区别
CRF、HMM原理 公式、维特比算法的公式
HMM做了哪些独立性假设
CRF的训练目标是什么？
CRF和深度学习的结合还知道哪些？
为什么Transformer self-attention可以替代seq2seq

### 文本生成
生成式问答解决生成句子多样性的方法
怎么评价生成效果的好坏
讲一下BLEU

### 思考题
1. 一个村子里重男轻女，生了女孩就继续生，直到生出男孩为止，试想在N年之后该村子的男女比例。答案：1:1
2. 有A（红）B（蓝）两瓶墨水，从A中取一勺倒到B中，混匀后，再从B中取等量墨水，倒到A中。问，是A中蓝墨水多还是B中红墨水多？
3. 一枚硬币，正反分布均匀，掷硬币，问至少多少次可以把这枚硬币的正面和反面都掷出来？
4. 一根绳子，允许你剪两刀，试问，剪完之后的绳子能构成三角形的概率是多少




### 算法
1. 稳定和非稳定的排序算法有哪些
2. 快速排序的最好，最坏，平均以及空间复杂度
3. 二分查找递归和非递归的时间和空间复杂度
4. 用两个栈实现一个队列操作，实现push、pop、len操作
5. 给定字符串 s ，求与 s 编辑距离为2的字符串集合。
6. 一个圆被分成M个扇形，一共有N种颜色，相邻扇形不同色，一共有几种涂法？
7. 有n枚硬币，每次从左边或右边拿一枚，一共拿m次，求能拿到的最高价值
8. 二叉搜索树转有序双向链表，要求不能创建新的节点
9. 统计一个十进制数字的二进制表示中有多少个一，用位运算写
10. 滑动窗口里的最大数
11. 给定一个乱序不重复数组，找到所有的三个数的集合，每个集合中 a + b + c == 0
12. 排序数组中绝对值不相等的元素个数，算法复杂度（M+N）
13. 搜索有序二维矩阵，复杂度
14. 层次遍历二叉树
15. 字符串的最长公共子串
16. 写一个函数判断一个字符串是否为回文串
17. 求一个字符串中的最长回文子串
#### 归并排序
![](https://s2.ax1x.com/2019/07/22/eiNXFS.md.png)
```python
def merge(a, b):
    c = []
    h = j = 0
    while j < len(a) and h < len(b):
        if a[j] < b[h]:
            c.append(a[j])
            j += 1
        else:
            c.append(b[h])
            h += 1
    if j == len(a):
        for i in b[h:]:
            c.append(i)
    else:
        for i in a[j:]:
            c.append(i)
    return c

def merge_sort(lists):
    if len(lists) <= 1:
        return lists
    middle = len(lists)//2
    left = merge_sort(lists[:middle])
    right = merge_sort(lists[middle:])
    return merge(left, right)

if __name__ == '__main__':
    a = [14, 2, 34, 43, 21, 19]
    print (merge_sort(a))
```
