---
title: HEXO博客迁移的步骤
tags: HEXO
mathjax: false
abbrlink: 27014
date: 2019-11-02 14:19:36
---
最近买了新电脑，但是博客是今天才迁移过来。本以为很麻烦，实际上操作非常简单，只需要三个步骤。
### 拷贝博客文件夹
大概50多M的样子，原封不动的拷贝到新电脑上。
### 安装hexo
```bash
$ npm install -g hexo-cli
```
有时候会出现`warning: LF will be replaced by CRLF in 
`的警告，输入下面的命令解决：
```bash
git config --global core.autocrlf false
```
### 重新配置ssh密钥
```bash
 ssh-keygen
 cat ~/.ssh/id_rsa.pub
```
复制到github ssh里面就行了。