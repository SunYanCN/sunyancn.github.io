---
title: 在Pypi上发表自己的Python库
categories: 工具
tags: pypi
abbrlink: 59741
date: 2018-11-28 10:17:38
---
![](https://i.loli.net/2018/11/28/5bfdfc9b41bb7.png)
## 推荐两篇很好的文章
 - [python核心 - 打包与发布](https://www.xncoding.com/2015/10/26/python/setuptools.html)
 - [使用ReadtheDocs托管文档](https://www.xncoding.com/2017/01/22/fullstack/readthedoc.html)


## 准备
### 注册账号
很显然地要在Pypi上注册一个账号,记住账号和密码。
<!--more-->
### 安装必要的库
#### setuptools
原则上安装了pip的环境都有setuptools,但并不影响你去尝试升级一下它。
`pip install --upgrade setuptools`
#### twine
这是一个简化将库发布到Pypi上流程的工具，具体的使用之后会讲到。
`pip install --upgrade setuptools`
#### 克隆仓库
大名鼎鼎的requests库的作者大神kennethreitz为大家准备了一个仓库作为一个setup.py的很好的模板，当然你也可以自己手写setup.py。
`git clone  https://github.com/kennethreitz/setup.py`
## 编码
### 编辑setup.py
直接编辑之前的仓库里的setup.py， 只需要修改一些必要的配置就可以了。
### 编写核心代码
接下来我们就可以编写自己的代码了，要注意源代码文件夹（仓库里的my_package文件夹）的名字与setup.py里配置的包名（Name）要一致。
另外在重新上传之前我们要修改__version__.py里的版本号，以免覆盖了以前的上传。
最终的代码结构:
```shell
│  LICENSE
│  MANIFEST.in
│  README.rst
│  setup.py
│
└─condition_chain
        core.py
        __init__.py
        __version__.py
```
### 打包上传
在setup.py的同级目录下运行以下命令。
`python setup.py sdist bdist_wheel`
然后运行`twine upload dist/*`
注意要输入Pypi账号和密码。
## 大功告成
之后我们就可以在Pypi里搜索到自己的Python库了，当然也可以直接通过pip安装。