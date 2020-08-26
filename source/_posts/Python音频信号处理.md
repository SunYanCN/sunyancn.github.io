---
title: Python音频信号处理
tags:
  - Python
  - 音频信号处理
mathjax: true
abbrlink: 20005
date: 2019-09-10 13:16:37
---

> 本文主要是对网上的一些文章的总结，参考的文章在文末已经列出

音频信号是模拟信号，我们需要将其保存为数字信号，才能对语音进行算法操作，WAV是Microsoft开发的一种声音文件格式，通常被用来保存未压缩的声音数据。

语音信号有三个重要的参数：**声道数**、**取样频率**和**量化位数**。

- **声道数**：可以是单声道或者是双声道
- **采样频率**：一秒内对声音信号的采集次数，44100Hz采样频率意味着每秒钟信号被分解成44100份，如果采样率高，那么媒体播放音频时会感觉信号是连续的。
- **量化位数**：用多少bit表达一次采样所采集的数据，通常有8bit、16bit、24bit和32bit等几种

如果你需要自己录制和编辑声音文件，推荐使用Audacity(http://audacity.sourceforge.net)， 它是一款开源的、跨平台、多声道的录音编辑软件。

<!--more-->

## 音频信号读取
```python
from scipy.io import wavfile
import numpy as np
import matplotlib.pylab as plt
samplimg_freq, audio = wavfile.read("data/input_freq.wav")
plt.plot(np.arange(audio.shape[0]),audio)
plt.show()
```
音频的时域信号波形：
![](https://i.loli.net/2019/09/10/taJsWjAQe21xb6g.png)

语音信号是一个非平稳的时变信号，但语音信号是由声门的激励脉冲通过声道形成的，而声道(人的口腔、鼻腔)的肌肉运动是缓慢的，所以“短时间”(10-30ms)内可以认为语音信号是平稳时不变的。由此构成了语音信号的“短时分析技术”。
在短时分析中，将语音信号分为一段一段的语音帧，每一帧一般取10-30ms，我们的研究就建立在每一帧的语音特征分析上。
提取的不同的语音特征参数对应着不同的语音信号分析方法：时域分析、频域分析、倒谱域分析...由于语音信号最重要的感知特性反映在功率谱上，而相位变化只起到很小的作用，所有语音频域分析更加重要。
## 预加重
预增强以帧为单位进行，目的在于加强高频。去除口唇辐射的影响，增加语音的高频分辨率。因为高频端大约在800Hz以上按6dB/oct (倍频程)衰减，频率越高相应的成分越小，为此要在对语音信号进行分析之前对其高频部分加以提升，也可以改善高频信噪比。k是预增强系数，常用0.97。

```python
pre_emphasis = 0.97
emphasized_signal = np.append(audio[0], audio[1:] - pre_emphasis * audio[:-1])
plt.plot(np.arange(emphasized_signal.shape[0]),emphasized_signal)
plt.show()
```
![](https://i.loli.net/2019/09/10/hycELdZWD5qo2AH.png)

## 分帧
分帧是将不定长的音频切分成固定长度的小段。为了避免窗边界对信号的遗漏，因此对帧做偏移时候，帧间要有帧移(帧与帧之间需要重叠一部分)，帧长(wlen) = 重叠(overlap)+帧移(inc)。inc为帧移，表示后一帧第前一帧的偏移量，fs表示采样率，fn表示一段语音信号的分帧数。
$$\frac{N-overlap}{inc}=\frac{N-wlen+inc}{inc}$$

通常的选择是帧长25ms（下图绿色），帧移为10ms（下图黄色）。接下来的操作是对单帧进行的。要分帧是因为语音信号是快速变化的，而傅里叶变换适用于分析平稳的信号。帧和帧之间的时间差常常取为10ms，这样帧与帧之间会有重叠（下图红色），否则，由于帧与帧连接处的信号会因为加窗而被弱化，这部分的信息就丢失了。
![](https://i.loli.net/2019/09/10/QA89CdDfL3qibS6.png)

## 语音信号的短时频域处理
在语音信号处理中，在语音信号处理中，信号在频域或其他变换域上的分析处理占重要的位置，在频域上研究语音可以使信号在时域上无法表现出来的某些特征变得十分明显，一个音频信号的本质是由其频率内容决定的，将时域信号转换为频域信号一般对语音进行**短时傅里叶变换**。

## python_speech_features
python_speech_features的比较好用的地方就是自带预加重参数，只需要设定preemph的值，就可以对语音信号进行预加重，增强高频信号。
python_speech_features模块提供的函数主要包括两个：MFCC和FBank。API定义如下：
> python_speech_features.base.fbank(signal, samplerate=16000, winlen=0.025, winstep=0.01, nfilt=26, nfft=512, lowfreq=0, highfreq=None, preemph=0.97, winfunc=<function <lambda>>)

从一个音频信号中计算梅尔滤波器能量特征,返回：2个值。第一个是一个包含着特征的大小为nfilt的numpy数组，每一行都有一个特征向量。第二个返回值是每一帧的能量。

> python_speech_features.base.logfbank(signal, samplerate=16000, winlen=0.025, winstep=0.01, nfilt=26, nfft=512, lowfreq=0, highfreq=None, preemph=0.97)

从一个音频信号中计算梅尔滤波器能量特征的对数,返回： 一个包含特征的大小为nfilt的numpy数组，每一行都有一个特征向量

参数
```python
参数：

signal - 需要用来计算特征的音频信号，应该是一个N*1的数组

samplerate - 我们用来工作的信号的采样率

winlen - 分析窗口的长度，按秒计，默认0.025s(25ms)

winstep - 连续窗口之间的步长，按秒计，默认0.01s（10ms）

numcep - 倒频谱返回的数量，默认13

nfilt - 滤波器组的滤波器数量，默认26

nfft - FFT的大小，默认512

lowfreq - 梅尔滤波器的最低边缘，单位赫兹，默认为0

highfreq - 梅尔滤波器的最高边缘，单位赫兹，默认为采样率/2

preemph - 应用预加重过滤器和预加重过滤器的系数，0表示没有过滤器，默认0.97

ceplifter - 将升降器应用于最终的倒谱系数。 0没有升降机。默认值为22。

appendEnergy - 如果是true，则将第0个倒谱系数替换为总帧能量的对数。

winfunc - 分析窗口应用于每个框架。 默认情况下不应用任何窗口。 你可以在这里使用numpy窗口函数 例如：winfunc=numpy.hamming
```

## MFCC特征和过滤器特征
```python
from python_speech_features import mfcc, logfbank

#提取MFCC特征和过滤器特征
mfcc_features = mfcc(audio, samplimg_freq)
filterbank_features = logfbank(audio, samplimg_freq)

#打印参数，查看可生成多少个窗体：
print('\nMFCC:\nNumber of windows =', mfcc_features.shape[0])
print('Length of each feature =', mfcc_features.shape[1])
print('\nFilter bank:\nNumber of windows=', filterbank_features.shape[0])
print('Length of each feature =', filterbank_features.shape[1])

#将MFCC特征可视化。转置矩阵，使得时域是水平的。
mfcc_features = mfcc_features.T
plt.matshow(mfcc_features)
plt.title('MFCC')

#将滤波器组特征可视化。转置矩阵，使得时域是水平的。
filterbank_features = filterbank_features.T
plt.matshow(filterbank_features)
plt.title('Filter bank')
plt.show()
```
输出如下：
```python
MFCC:
Number of windows = 42
Length of each feature = 13

Filter bank:
Number of windows= 42
Length of each feature = 26
```
![](https://i.loli.net/2019/09/10/9PzrY5gIXxTiM7U.png)
![](https://i.loli.net/2019/09/10/rEyK4ZjAeOLDmC6.png)
## 触发词检测
![unhpCt.png](https://s2.ax1x.com/2019/09/26/unhpCt.png)
## Reference
- https://jingyan.baidu.com/article/1709ad804e575b4634c4f0b1.html
- https://zhuanlan.zhihu.com/p/57004884
- https://python-speech-features.readthedocs.io/en/latest/
- https://www.meiwen.com.cn/subject/ahyxuqtx.html
- https://haythamfayek.com/2016/04/21/speech-processing-for-machine-learning.html
- https://www.cnblogs.com/LXP-Never/p/10078200.html#%E9%9F%B3%E9%A2%91%E4%BF%A1%E5%8F%B7%E7%9A%84%E8%AF%BB%E5%86%99%E3%80%81%E6%92%AD%E6%94%BE%E5%8F%8A%E5%BD%95%E9%9F%B3
- https://github.com/majianjia/nnom/tree/master/examples/keyword_spotting
