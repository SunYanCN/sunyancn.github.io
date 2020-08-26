---
title: ReadDocs
categories: 工具
tags: ReadDocs
abbrlink: 41605
date: 2018-11-11 22:14:49
---

![](https://i.loli.net/2018/08/18/5b777783c90fb.png)
## 搭建工具
Sphinx 是一个基于 Python 的文档生成工具，最早只是用来生成 Python 官方文档，随着工具的完善，越来越多的知名的项目也用他来生成文档，甚至完全可以用他来写书。例如我最近看到tensorly等库都有包括Github主页，Jupyter Notebook,以及Read the Docs代码API说明。 
<!--more--> 
## 搭建步骤 
1. `pip install sphinx sphinx-autobuild sphinx_rtd_theme` 
2. 新建一个Github仓库，并拉到本地，仓库名设为项目名。 
3. 命令行进入仓库本地目录，输入`sphinx-quickstart`   ![](https://i.loli.net/2018/08/18/5b7779ccd6682.png)   这个是项目的配置文件，这里要改动的是项目名，版本，以及语言。其他的默认即可。生成下面目录结构。   ![](https://i.loli.net/2018/08/18/5b777a3abcaba.png) 
4. 进入目录下面的conf.py，可以看到我们刚才的配置的内容就是在配置这个文件。下面继续修改。   首先把源文件改为使用习惯的Markdown文件，`source_suffix = \'.md\'`   然后把主题换了，`html_theme = \'sphinx_rtd_theme\'。`   最后把LaTeX内容配置一下。   ![](https://i.loli.net/2018/08/18/5b777b951d64b.png) 
5. 进入命令行 make html   ![](https://i.loli.net/2018/08/18/5b777bdb70e1a.png) 
6. 提交目录到Github。 
7. 打开Read the Docs,并使用Github注册，导入项目并点击阅读文档即可。   也可以在GitHub 里选择仓库，然后依次点击 Setting => Webhooks & Service => Add service => ReadTheDocs,激活这个选项。

## 参考文章 

1. [sphinx中文翻译手册](https://zh-sphinx-doc.readthedocs.io/en/latest/contents.html) 
2. http://dwz.cn/wEwYNed3 
3. https://www.jianshu.com/p/78e9e1b8553a