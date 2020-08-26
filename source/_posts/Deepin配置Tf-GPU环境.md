---
title: Deepin配置TF GPU环境
categories: 工具
tags: GPU
abbrlink: 32924
date: 2018-10-26 14:19:55
---
## 更新
2018年10月29日更新。

使用conda安装GPU加速版本的TensorFlow时，将自动安装GPU支持所需的CUDA和CuDNN库。不需要再额外安装这些库了。而且与 pip 安装相比，conda 安装可以带来超过8倍的CPU速度提升。因此推荐通过conda安装TensorFlow。
## 前言
服务器在升级维护，打算先在本机上做Mask RCNN，之前一直懒得去折腾这些，这次一次搞清楚了，记录一下。
总体参照的是下面这篇博客：
https://blog.csdn.net/aaronjny/article/details/79330727

## 第一步：检查
我自己电脑上是有GPU的，查看方式是安装nvidia-smi

```python
sudo apt-get install nvidia-smi
```


直接在终端运行nvidia-smi输出GPU信息。
![](https://i.loli.net/2018/10/26/5bd2b390e7141.png)

然后检查有没有gcc，gcc --version,输出gcc版本
![](https://i.loli.net/2018/10/26/5bd2b3efa0247.png)
<!--more-->
## 安装cuda
一句话完成：
```python
sudo apt install nvidia-cuda-dev nvidia-cuda-toolkit nvidia-nsight nvidia-visual-profiler 
```


安装的时候能看到安装的是什么版本，比如我这里是9.1。
![](https://i.loli.net/2018/10/26/5bd2b49c8e34e.png)
安装完有一个警告，再安装一下这个：
sudo apt-get install console-setup
## 安装cudnn
首先下载cudnn,https://developer.nvidia.com/cudnn
对压缩包进行解压，得到一个名为cuda的文件夹。
然后执行下面的步骤：
- 第一，将解压出来的cuda/include/下的cudnn.h文件复制到/usr/local/include/目录下。
- 第二，将cuda/lib64/目录下的所有文件复制到python环境的tensorflow包的tensorflow/python/目录下。


## 安装Tf GPU版本
```python
conda install tensorflow-gpu
```

## 测试
![](https://i.loli.net/2018/10/26/5bd2b58ec9c6c.png)
正常输出显卡信息则安装完成。