---
title: >-
  《Retrieve-and-Read,Multi-task Learning of Information Retrieval and Reading
  Comprehension》的References和Citations梳理
tags: 论文阅读
mathjax: false
abbrlink: 37274
date: 2019-11-24 22:07:58
---
### Citations
1. [Retrieval-Based Open-Domain Question Answering: Exploring The Impact on The Retrieval Component across Datasets》](https://www.semanticscholar.org/paper/Retrieval-Based-Open-Domain-Question-Answering%3A-The-Jiang/77c55d771bca41ee7361dba3a27fbc883bba65bf)
    论文动机：主要探讨的是数据集对IR组件的影响，有一些比较好的综述性的内容：

    <img src="https://s2.ax1x.com/2019/11/25/MjZU9P.png" alt="MjZU9P.png" style="zoom:67%;" />
    
    <!--more-->
    
    <img src="https://s2.ax1x.com/2019/11/25/MjZR3V.png" alt="MjZR3V.png" style="zoom:67%;" />
    
    <img src="https://s2.ax1x.com/2019/11/25/MjZOgK.png" alt="MjZOgK.png" style="zoom:67%;" />
    
    Text Span的评估指标：
    
    > For text-span questions whose answer is string(s), we need to compare the predicted string(s) with the ground truth answer string(s) (i.e., the correct answer). RCstyle QA task generally uses evaluation metrics Exact Match (EM) and F1 score (F1) proposed by Rajpurkar et al. [94] for text-span questions [104, 116]. EM assigns credit 1.0 to questions whose predicted answer is exactly the same as the ground truth answer and 0.0 otherwise, so the computation of EM is the same as the metric Accuracy but for different categories of RC-style QA. F1 measures the average word overlap between the predicted answer and the ground truth answer. These two answers are both considered as bag of words with lower cases and ignored the punctuation and articles "a", "an" and "the". For example, the answer "The Question Answering System" is treated as a set of words {question, answering, system}. Therefore, F1 of each text-span question can be computed at word-level by Equation 2.2  
    > $$
    > F 1=\frac{2 \times \text {Precision} \times \text {Recall}}{\text {Precision}+\text {Recall}}
    > $$
    > $$
    > \begin{array}{l}{\text { Precision }=\frac{\#\{\text { predicted words }\} \cap\{\text { ground truth words }\}}{\text { # predicted words }}} \\ {\text { Recall }=\frac{\#\{\text { predicted words }\} \cap\{\text { ground truth words }\}}{\# \text { ground truth words }}}\end{array}
    >$$
    
2. [《Answering Complex Open-domain Questions Through Iterative Query Generation》](https://arxiv.org/abs/1910.07000)
   **论文信息**：EMNLP-IJCNLP 2019. Xiaowen Lin, Leo Mehr, and Zijian Wang contributed equally. [GitHub](https://github.com/qipeng/golden-retriever)
   **论文动机**：对于当前的单步检索和问答系统（QA）来说，回答“ Armada的作者的哪部小说将改编为Steven Spielberg的故事片？”等问题具有挑战性。因为该问题很少包含有关缺失实体（此处为作者）的可检索线索。要回答这一问题，需要进行多跳推理，其中必须收集有关缺失实体（或事实）的信息才能继续进行推理。
   **论文主要实现方法**：设计了一个IR，在读取上下文和检索更多支持文档之间进行迭代，以回答开放域多跳问题。主要是对问题进行分解，涉及到新的问题的生成。
   ![M81WEq.md.png](https://s2.ax1x.com/2019/11/12/M81WEq.md.png)
   
3. 《Answering while Summarizing: Multi-task Learning for Multi-hop QA with Evidence Extraction》

   **论文信息**：ACL2019
   **论文动机**： 使用阅读理解（RC）进行的问答（QA）引起了广泛的关注。这项研究的重点是可解释的多跳问答的解释性，该任务要求系统通过推理和收集不连贯的参考文本来返回带有证据句的答案。本文提出了用于查询证据的查询集中提取器（QFE）模型，并将多任务学习与QA模型结合使用。QFE模型是受到提取摘要模型的启发，与现有的独立提取每个证据语句的方法相比，该方法使用带有注意机制的RNN对问题语句进行顺序提取证据语句，因此使得QFE能够考虑证据句子之间的依存关系，并覆盖问题句子中的重要信息。实验结果表明，简单RC基线模型的QFE在HotpotQA上获得了最高的证据提取得分。
   **论文主要实现方法**：第一张图讲的是整体的模型设计，重点是添加了一个QFE模块。模型设计包括首先做Q和CEmbedding，之后分别输入Bi RNN编码，编码的信息进行Bi Attention，再输入Bi RNN，经过self Attention之后，输出经过Bi RNN和全连接层输出。(这里我怀疑他这个模型可能会Train不动，堆了太多层RNN，训练时间大大增加)。第二张图是QFE模块，说是来自于一篇生成的文章，看不太懂，跳过。

  <img src="https://s2.ax1x.com/2019/11/25/MXjiLT.md.png" alt="MXjiLT.md.png" style="zoom:67%;" />

  <img src="https://s2.ax1x.com/2019/11/25/MXjW60.md.png" alt="MXjW60.md.png" style="zoom:67%;" />

4. 《Context Attentive Document Ranking and Query Suggestion》

   **论文信息**：  SIGIR 2019 

   **论文动机**： 提出了一种上下文感知的Ranking模型，用于用户的搜索并增强检索性能。引入了两级分层递归神经网络，通过联合优化两个伴随检索任务：文档排名和查询建议，来学习单个查询，搜索任务和相应的依存关系的搜索上下文表示。为了确定搜索上下文和用户正在进行的搜索活动之间的变量依赖关系结构，在递归状态的两个级别上都引入了注意力机制。针对基线方法进行了很多实验比较，并进行了消融分析，证实了提出的方法在搜索任务中非常有价值。

   **论文主要实现方法**： 

   <img src="https://s2.ax1x.com/2019/11/25/Mj98Tf.png" alt="Mj98Tf.png" style="zoom:67%;" />

5. 《 Effective Subword Segmentation for Text Comprehension 》

   **论文信息**：IEEE/ACM Transactions On Audio, Speech, And Language Processing2018

   **论文动机**：表征学习是机器阅读理解和推理的基础。在最新模型中（应该指的是BERT系列），字符级表示已被广泛采用，用来缓解有效表示稀有或复杂单词的问题。但是，由于忽略单词内部连续字符的语言连贯性，字符本身并不是用于表示或者叫词嵌入的自然最小语言单元。本文提出了一个通用的子词增强嵌入框架，用于学习和组成计算派生的子词级表示形式。

   **论文主要实现方法**：<img src="https://s2.ax1x.com/2019/11/25/MjCL5T.png" alt="MjCL5T.png" style="zoom:67%;" />

6. 《Learning to Attend On Essential Terms: An Enhanced Retriever-Reader Model for Open-domain Question Answering 》

   **论文信息**：NAACL-HLT2018

   **论文动机**：

   **论文主要实现方法**：

7. 《 Multi-style Generative Reading Comprehension》

   **论文信息**：ACL2019

   **论文动机**：

   **论文主要实现方法**：

8. 《Real-Time Open-Domain Question Answering with Dense-Sparse Phrase Index》

   **论文信息**：ACL2019

   **论文动机**：

   **论文主要实现方法**：

9. 《Regularization Advantages of Multilingual Neural Language Models for Low Resource Domains》

   **论文信息**：

   **论文动机**：

   **论文主要实现方法**：

10. 《Controlling Risk of Web Question Answering》

    **论文信息**： SIGIR2019

    **论文动机**： 问答（QA）已成为现代搜索系统中必不可少的组件，它可以通过提供对用户信息需求的直接答案来显着改善用户的搜索体验。可以通过在检索的段落上应用机器阅读理解（MRC）模型以提取有关搜索查询的答案来实现。随着深度学习技术的发展，最新的深度方法已实现了最先进的MRC性能。但是，现有的关于MRC的研究很少涉及预测不确定性问题，即MRC模型的预测错误的可能性有多大，从而导致现实Web QA应用程序中的风险不可控制。在这项工作中，我们首先对Web质量检查的风险进行了深入的调查。然后，我们介绍一个新颖的风险控制框架，它由使用探查思路进行不确定性估计的合格模型和用于选择性输出的决策模型组成。为了进行评估，我们引入了与风险相关的指标，而不是MRC中的传统EM和F1来评估具有风险意识的Web质量检查。实际的Web质量检查数据集和MRC学术基准收集的经验结果证明了我们方法的有效性。 

    **论文主要实现方法**：![MjPQdP.png](https://s2.ax1x.com/2019/11/25/MjPQdP.png)

    ![MjiPyj.png](https://s2.ax1x.com/2019/11/25/MjiPyj.png)

    <img src="https://s2.ax1x.com/2019/11/25/Mji8Tx.png" alt="Mji8Tx.png" style="zoom:50%;" />

    

    

