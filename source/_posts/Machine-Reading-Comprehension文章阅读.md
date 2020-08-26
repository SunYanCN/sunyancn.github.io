---
title: Machine Reading Comprehension文章阅读
tags: 论文
mathjax: false
abbrlink: 21734
date: 2019-11-15 21:23:16
---
### 《Relation Module for Non-Answerable Predictions on Reading Comprehension》
> ACL 2019 paper, [论文地址](https://www.aclweb.org/anthology/K19-1070/)

#### 摘要
由于具有挑战性的阅读理解数据集的增加，机器阅读理解（MRC）近来引起了大量研究关注。在本文中，我们旨在提高MRC模型确定问题在给定上下文中是否具有答案的能力（例如，最近提出的SQuAD 2.0任务）。关系模块包括语义提取和关系信息。我们首先使用多头自我关注池从问题和上下文中提取高级语义作为对象。然后将这些语义对象传递到关系网络，该关系网络为句子中每个对象对生成关系得分。这些分数用于确定问题是否无法回答。我们使用BiDAF和BERT模型作为基线读取器，在SQuAD 2.0数据集上测试关系模块。在BiDAF阅读器上，我们获得F1精度的1.8％增益，在BERT基本模型上，获得1.0％的F1精度。这些结果表明我们的关系模块在MRC上的有效性。

### 




