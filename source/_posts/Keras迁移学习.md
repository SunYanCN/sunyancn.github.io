---
title: Keras迁移学习
categories: 项目
tags:
  - 迁移学习
  - cnn
abbrlink: 60371
date: 2018-11-03 13:21:04
---
## 迁移学习
简单来说迁移学习是把在ImageNet等大型数据集上训练好的CNN模型拿过来，经过简单的调整应用到自己的项目上去。
![](https://i.loli.net/2018/11/03/5bdd5f94a2ad6.png)
<!--more-->
## 迁移学习的分类
迁移学习分为三种：
- 第一种叫transfer learning。用于图像分类的卷积神经网络由两部分组成：从一系列卷积层和池化层开始，并以全连接的分类器结束。第一部分称为模型的卷积基（convolutional base），即全连接层之前的卷积池化部分，特征提取就是利用预训练好的的网络模型的卷积基，运行新的数据，并在输出之上训练一个新的分类器（见图1.1）。因此，我们只需要训练分类器部分，卷积基直接用现成的不动。![](https://i.loli.net/2018/11/03/5bdd38ae95f2f.png)
为什么只重用卷积基？能使用相同的分类器吗？一般来说前面的卷积基提取了低级特征，这在很多其他类似问题是可以通用的。而最后的全连接层是与具体问题相关的高级特征，因此不太可复用。
- 第二种是fine tune，即微调，就是让一部分底层也参与训练。一般来说，只有在顶层的分类器已经被训练好之后，才去微调卷积基的顶层。
- 预训练模型。例如，Caffe库有一个model zoo，其他人可以在这里找到各种训练好的模型的checkpoint。

 一个典型的迁移学习过程是这样的。首先通过transfer learning对新的数据集进行训练，训练过一定epoch之后，改用fine tune方法继续训练，同时降低学习率。这样做是因为如果一开始就采用fine tune方法的话，网络还没有适应新的数据，那么在进行参数更新的时候，比较大的梯度可能会导致原本训练的比较好的参数被污染，反而导致效果下降。
## 微调经验总结
主要的因素是数据集的大小和原始数据集的相似性。有一点一定记住：网络前几层学到的是通用特征，后面几层学到的是与类别相关的特征。下面分情况讨论：
1.  **新数据集很小，与原始数据集类似。** 因为新数据集比较小，如果fine-tune可能会过拟合；又因为新旧数据集类似，所以可能高层特征类似，可以使用预训练网络当做特征提取器，用提取的特征训练线性分类器。
2.  **新数据集很大，与原始数据集类似。** 可以微调，不用担心过拟合。
3.  **新数据集很小但与原始数据集非常不同。** 新数据集小，最好不要fine-tune，和原数据集不类似，最好也不使用高层特征。这时可是使用前面层的特征来训练SVM分类器。
4.  **新数据集很大，与原始数据集非常不同。** 因为新数据集足够大，可以重新训练。但是实践中fine-tune预训练模型还是有益的。新数据集足够大，可以fine-tine整个网络。
![](https://i.loli.net/2018/11/03/5bdd5fc54c2ca.png)
## 代码步骤
### 加载数据
这一步很正常，主要是处理图片数据和划分数据集
### 加载MobileNetV2模型（不含全连接层）
Keras的应用模块Application提供了带有预训练权重的Keras模型，这些模型可以用来进行预测、特征提取和finetune。你可以从keras.applications模块中导入它。
```python
base_model = MobileNetV2(weights='imagenet', include_top=False)
```
### 添加新的顶层
```python
def add_new_last_layer(base_model, nb_classes):
    x = base_model.output
    x = GlobalAveragePooling2D()(x)
    # GlobalAveragePooling2D 将 MxNxC 的张量转换成 1xC 张量，C是通道数
    x = Dense(FC_SIZE, activation='relu')(x)
    predictions = Dense(nb_classes, activation='softmax')(x)
    model = Model(input=base_model.input, output=predictions)
    return model
```
### 训练顶层分类器
冻结base_model所有层，然后进行训练。
```python
def setup_to_transfer_learn(model, base_model):
    for layer in base_model.layers:
        layer.trainable = False
    model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])
    
setup_to_transfer_learn(model, base_model)
```
其实这一步也可以和上一步结合起来写，更加简洁：
```python
from keras import models
from keras import layers
# 在conv_base的基础上添加全连接分类网络
conv_base = MobileNetV2(weights='imagenet', include_top=False)
conv_base.trainable = False
model = models.Sequential()
model.add(conv_base)
model.add(layers.Flatten())
model.add(layers.Dense(256, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))
```
### 对顶层分类器进行fine_tuning
冻结部分层，对顶层分类器进行Fine-tune

Fine-tune以一个预训练好的网络为基础，在新的数据集上重新训练一小部分权重。fine-tune应该在很低的学习率下进行。
```python
def setup_to_finetune(model):
    for layer in model.layers[:NB_MobileNetV2_LAYERS_TO_FREEZE]:
        layer.trainable = False
    for layer in model.layers[NB_MobileNetV2_LAYERS_TO_FREEZE:]:
        layer.trainable = True
    model.compile(optimizer=SGD(lr=0.0001, momentum=0.9), loss='categorical_crossentropy', metrics=['accuracy'])
```
这里可能比较疑惑的是NB_MobileNetV2_LAYERS_TO_FREEZE是多少呢，怎么找呢。方法是利用Pycharm的Debug功能，查看base_model.layers中的值。
当然也可以选择使用layer name来进行选择：
```python
conv_base.trainable = True

set_trainable = False
for layer in conv_base.layers:
    if layer.name == 'block5_conv1':
        set_trainable = True
    if set_trainable:
        layer.trainable = True
    else:
        layer.trainable = False
```
## 总体代码
```python
from keras.applications import MobileNetV2
from keras import layers
from keras.models import Model
from keras.optimizers import SGD
from keras.utils import plot_model

FC_SIZE = 256
IM_WIDTH, IM_HEIGHT = 28, 28
nb_classes = 100
NB_MobileNetV2_LAYERS_TO_FREEZE = 149

def add_new_last_layer(base_model, nb_classes):
    x = base_model.output
    x = layers.GlobalAveragePooling2D()(x)
    x = layers.Dense(FC_SIZE, activation='relu')(x)
    predictions = layers.Dense(nb_classes, activation='softmax')(x)
    model = Model(inputs=base_model.input, outputs=predictions)
    return model

def setup_to_transfer_learn(model, base_model):
    for layer in base_model.layers:
        layer.trainable = False
    model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])

def setup_to_finetune(model):
    for layer in model.layers[:NB_MobileNetV2_LAYERS_TO_FREEZE]:
        layer.trainable = False
    for layer in model.layers[NB_MobileNetV2_LAYERS_TO_FREEZE:]:
        layer.trainable = True
    model.compile(optimizer=SGD(lr=0.0001, momentum=0.9), loss='categorical_crossentropy', metrics=['accuracy'])


if __name__ == '__main__':

    base_model = MobileNetV2(weights='imagenet', include_top=False)
    model = add_new_last_layer(base_model, nb_classes)
    setup_to_transfer_learn(model, base_model)
    model.fit()
    setup_to_finetune(model)
    model.fit()
    print(model.summary())
    plot_model(model, to_file='mobilev2.png', show_shapes=True)
```
## 总结
- 在小数据集上，过拟合将是主要问题。数据扩充是处理图像数据时过拟合的强大方法。
- 通过卷积基特征提取可以利用先前学习的特征。
- 作为特征提取的补充，我们可以使用微调来适应新的问题。
## Reference
1. [使用Inception V3模型进行迁移学习](https://blog.csdn.net/tsyccnh/article/details/78889838)
2. [基于InceptionV3模型的迁移学习应用](https://blog.csdn.net/starter_____/article/details/79369382)
3. [Keras Demo](https://github.com/Zheng-Wenkai/Keras_Demo)
4. [在小数据集上迁移学习(上)](https://zhuanlan.zhihu.com/p/33346258)
5. [在小数据集上迁移学习(下)](https://zhuanlan.zhihu.com/p/33363701)
6. [CS231N:迁移学习](http://cs231n.github.io/transfer-learning/)