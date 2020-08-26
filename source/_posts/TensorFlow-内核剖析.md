---
title: TensorFlow 内核剖析
categories: 笔记
tags: Tensorflow
abbrlink: 36490
date: 2018-12-06 20:05:14
---
这是我找的一个Tensorflow的书，作者是[刘光聪](https://www.jianshu.com/p/fda4ae1e2547)。书写的非常不错，我也借此机会学习一波。书中的TensorFlow使用的是1.2版本，目前来说算是很新的。
作者在前言里面写到：
>这是一本剖析 TensorFlow 内核工作原理的书籍，并非讲述如何使用 TensorFlow 构建机器学习模型，也不会讲述应用 TensorFlow 的最佳实践。本书将通过剖析 TensorFlow 源代码的方式，揭示 TensorFlow 的系统架构、领域模型、工作原理、及其实现模式等相关内容，以便揭示内在的知识。

可以看出，这必定是一本干货满满的书。[Github](https://github.com/horance-liu/tensorflow-internals-errors)。
<!--more-->
# 基础知识
## 基础知识
### 前身
Tensorflow的前身是DistBelief。DistBelief 使用参数服务器 (Parameter Server, 常称为 PS) 的系统架构，训练作业包括两个分离的进程：无状态的 Worker 进程，用于模型的训练；有状态的 PS 进程，用于维护模型的参数。如图1-1所示，在分布式训练过程中，各个模型副本异步地从 PS 上拉取训练参数 w，当完成一步迭代运算后，推送参数的梯度 ∆w 到 PS 上去，并完成参数的更新。
![](/TensorFlow-内核剖析/20181206081413795.png)
由于其缺乏足够的灵活性和可扩展性，TensorFlow 应运而生，开创了深度学习领域的新纪元。
### 设计原则
- 延迟计算：图的构造与执行分离，并推迟计算图的执行过程；
- 原子 OP：OP 是最小的抽象计算单元，支持构造复杂的网络模型；
- 抽象设备：支持 CPU, GPU, ASIC 多种异构计算设备类型；
- 抽象任务：基于任务的 PS，对新的优化算法和网络模型具有良好的可扩展性。

### 优势
![](/TensorFlow-内核剖析/20181206081721146.png)

### 开源
2015.11正式开源，并产生巨大影响。
![](/TensorFlow-内核剖析/20181206081832279.png)

## 编程环境
### 代码统计
截止当前最新发布的 1.4 版本，TensorFlow 代码库拥有大约 100 万代码。其中，包括53 万行 C/C++ 代码，37 万行 Python 代码，而且代码规模在不断膨胀之中。其中，Python提供的 API 是最完善的；相比之下，其他编程语言的 API 尚未成熟，甚至处于起步阶段。
![](/TensorFlow-内核剖析/20181206081932974.png)
### 技术栈
![](/TensorFlow-内核剖析/20181206082100122.png)

## 基础概念
### Softmax
### 交叉熵
### Placeholder




