---
title: Linux使用百度云下载
tags: 百度云
mathjax: false
abbrlink: 17514
date: 2019-11-06 18:46:35
---
### 前言
项目github地址：https://github.com/iikira/BaiduPCS-Go

### 安装
- 下载到本地：[3.6版本](https://github.com/iikira/BaiduPCS-Go/releases/download/v3.6/BaiduPCS-Go-v3.6-linux-386.zip)
- scp传输到服务器：`scp -P 2222 C:\Users\lenovo\Pictures\BaiduPCS-Go-v3.6-linux-386.zip root@127.0.0.1:/home`
- 解压：`unzip BaiduPCS-Go-v3.6-linux-386.zip`
询问是否覆盖：y
进入目录：`cd BaiduPCS-Go-v3.6-linux-386`
启动：`./BaiduPCS-Go`

### 登录
- 登录：`login`
- 然后按照提示输入用户名，密码。回车后，会让输入验证码。会提示打开浏览器查看验证码，可能试验登录多次，才正确。

然后让你输入验证手机号，会给你发短信，输入短信的验证码后，就能登录了。

### 操作
- 登录进去后，查看有哪些百度网盘命令：`help`
- 列出百度网盘的目录：`ls`
- 下载百度网盘的文件： `d 文件名`
- 下载完成后，会提示你，把文件下载到哪个目录了。 一般在 根目录下，点进去`root`文件夹，即`/root/Downloads/`
- 修改默认储存路径：`BaiduPCS-Go config set -savedir download`
- 退出程序:运行命令 `quit` 或 `exit` 或 组合键 `Ctrl+C` 或 组合键 `Ctrl+D`
- 下载大文件：`./BaiduPCS-Go config set -user_agent=""`

### 退出
退出当前登录的百度帐号：`logout`



