---
title: Tensorflow Object_Detection
categories: 项目
tags: 目标检测
abbrlink: 43522
date: 2018-10-27 14:38:59
---

![下载 (1).png](https://i.loli.net/2018/10/28/5bd5c32f8f6ef.png)
## 准备
准备大体按照官方[文档](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md)进行。
<!--more-->
### 检查库
```shell
sudo apt-get install protobuf-compiler python-pil python-lxml python-tk
pip install --user Cython
pip install --user contextlib2
pip install --user jupyter
pip install --user matplotlib
```
### COCO API installation
```shell
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
```
### 下载Object_Detection
[官方链接](https://github.com/tensorflow/models/tree/master/research/object_detection?1540622178700)在这里。这里我没有下载整个models，在linux下面只下载了Object_Detection。方法如下：
1. 首先安装svn
 ```shell
 sudo apt-get install subversion
 ```

2. 确定导出的文件夹URL,比如https://github.com/tensorflow/models/tree/master/research/object_detection?1540622178700 。
3. 首先改为：https://github.com/tensorflow/models/tree/master/research/object_detection
然后把其中的tree/master替换成trunk。
4. 导出
```shell
svn checkout https://github.com/tensorflow/models/trunk/research/object_detection
```
Reference：https://blog.csdn.net/why19940926/article/details/78580067
### Protobuf Compilation
文档里面有详细说明。这里我把Protobuf直接加入了路径，执行下面的操作：
```shell
protoc object_detection/protos/*.proto --python_out=.
```
最后看到protos文件夹下有python文件生成，如图;
![](https://i.loli.net/2018/10/27/5bd40b533b996.png)
### Add Libraries to PYTHONPATH
在Object_Detection的上一级目录下执行下面代码：
```shell
# From tensorflow/models/research/
export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
```
注意这种方式只对当前命令窗口有效，关掉窗口还是要重新输入，最好的方案是直接把包放到目录下去，比如我的：`/home/sunyan/anaconda3/lib/python3.6/site-packages/object_detection`
### Testing the Installation
还是在Object_Detection的上一级目录下执行下面代码
```shell
python object_detection/builders/model_builder_test.py
```
果不其然，报错了。我就知道没那么容易装好(┑(￣Д ￣)┍)。
![](https://i.loli.net/2018/10/27/5bd40d9a38c15.png)
说是TF里面没有keras模块，但我知道最新的是有的，所以检查一下TF的版本:
```python
import tensorflow as tf
print(tf.__versin__)
#1.3.0
```
查了一下TF1.4版本才有的keras,所以通过pip升级到最新的版本，这里我选择TF==1.5：
```shell
pip install tensorflow-gpu==1.5
```
重新执行，依旧有报错;
![](https://i.loli.net/2018/10/27/5bd40f111f18d.png)
然而我使用pycharm打开后运行通过了，有点奇怪。
### 运行notebook测试
因为我装的是TF1.5，所以首先把里面的这句话删掉：
```[python]
if StrictVersion(tf.__version__) < StrictVersion('1.9.0'):
  raise ImportError('Please upgrade your TensorFlow installation to v1.9.* or later!')
```
第一次运行需要下载模型，下载时间比较久，第二次就不需要了，可以把下载的代码注释掉。
```[python]
# opener = urllib.request.URLopener()
# opener.retrieve(DOWNLOAD_BASE + MODEL_FILE, MODEL_FILE)
```
当然也可以自己下载，{% btn https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md, 点击下载模型, download fa-lg fa-fw %}，经测试，自己下载的速度还快一点。
(2018.10.28 Update:我电脑的GPU环境被我搞乱了，目前只能在CPU上运行了)
(2018.10.28 Update:问题找到了，conda安装TF GPU版本的会自动下载cuda和cudnn，不需要额外安装了)

  
