---
title: BiliBili蒙版弹幕
categories: 项目
tags: CNN
abbrlink: 34384
date: 2018-10-26 13:59:27
---
## What is Mask RCNN

- Faster RCNN + Masks
- Developed by Facebook AI Research(FAIR)

 ![](https://i.loli.net/2018/10/26/5bd2bd96503da.png)
 <!--more-->

## Setup and Installation

- Step1: Python3.6
- Step2: Clone the Mask_RCNN repo

    ```python
    git clone https://github.com/matterport/Mask_RCNN.git
    ```

- Step3: Install the dependencies
  下载下来的Mask RCNN中有一个requirements.txt文件。

  ```python
  pip install -r requirements
  ```

- Step4: Install cocoapi

  ```python
  pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
  ```

- Step5: Dowload the Pre-trained Weights
  https://github.com/matterport/Mask_RCNN/releases/download/v2.0/mask_rcnn_coco.h5
  把下载的h5文件放入Mask-RCNN的文件夹里面

## Run
首先打开Mask_RCNN/samples notebook，运行。
出现两个错误：

- No module named 'imgaug'

  ```python
  pip install imgaug
  pip install opencv-python
  ```

  

- TypeError: softmax() got an unexpected keyword argument 'axis'
  keras 2.1.6的softmax没有axis这个参数了，回退keras版本：`pip install keras==2.1`

## 结果
![](https://i.loli.net/2018/10/26/5bd2bd104eeac.png)



## BiliBili蒙版弹幕

原版视频地址：[新垣结衣日清鸡肉拉面广告](https://www.bilibili.com/video/av16789504)

### 视频弹幕压缩

给视频加硬字幕是把字幕加到视频流中，不是单独的字幕流。

```shell
ffmpeg -i demo.mp4 -vf ass=subtitle.ass output.mp4
```
参考链接：https://blog.csdn.net/fallfollowernolisten/article/details/68489499

### 准备工作

1. 准备带弹幕的视频文件和不带弹幕的视频文件。
2. 配置视频文件路径。
3. 在coco文件下的`__init__.py`中加入代码：`from .coco import *` 。
4. 执行代码，等待完成。

### 执行程序
内存占用情况：
![](https://i.loli.net/2018/10/26/5bd304c51f8f6.png)
显卡使用情况：
![](https://i.loli.net/2018/10/26/5bd305046a62e.png)
可以看到还是很耗费显存的，我的电脑差一点就不够了，而且实际训练起来速度也很慢。

## 视频音频压缩
生成的蒙版弹幕视频文件没有声音，所以需要手动添加声音，还是用ffmpeg。
- 首先在把视频上传到[123app]()，使用Audio Convert来转换为MP3格式，就是音频文件了，文件大小大概有几M。
- 下一步是合并音视频。假设现有视频文件video.avi(包含声音) 和音频文件audio.mp3，要把video.avi中的视频和audio.mp3合并，步骤如下：
```shell
//将video.avi 中的视频提取到临时文件video2.avi中
ffmpeg -i video.avi -vcodec copy -an video2.avi 
//合并 video2.avi 和  audio.mp3 到output.avi
ffmpeg -i video2.avi -i audio.mp3 -vcodec copy -acodec copy output.avi 
//video2是临时文件，最后可以删除。
```
## 完成效果
![](https://i.loli.net/2018/10/27/5bd40075b2092.png)
## Reference

- [Mask-RCNN](https://github.com/matterport/Mask_RCNN)
- [Mask-RCNN-series](https://github.com/markjay4k/Mask-RCNN-series?1540532721233)
- [cocoapi](https://github.com/philferriere/cocoapi)
- [Mask RCNN with Keras and Tensorflow](https://www.bilibili.com/video/av23064129/?p=1)
- [Mask_Danmu](https://github.com/youyuge34/Mask_Danmu)