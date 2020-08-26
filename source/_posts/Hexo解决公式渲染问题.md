---
title: Hexo解决公式渲染问题
categories: 工具
tags: Hexo
abbrlink: 58604
date: 2018-11-14 20:45:32
---
## Next配置文件

```shell
mathjax:
  enable: True
  per_page: false
  cdn: //cdn.bootcss.com/mathjax/2.7.1/latest.js?config=TeX-AMS-MML_HTMLorMML
```

## 更换渲染引擎
更换 Hexo 的 markdown 渲染引擎，hexo-renderer-kramed 引擎是在默认的渲染引擎 hexo-renderer-marked 的基础上修改了一些 bug ，两者比较接近，也比较轻量级。


## 更改渲染设置
到博客根目录下，找到`node_modules\kramed\lib\rules\inline.js`，把第11行的` escape` 变量的值做相应的修改：
```shell
 //em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```
同时把第20行的em变量也要做相应的修改。
```shell
//em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```
重新启动hexo（先clean再generate）,问题完美解决。

<!--more-->

## 例子

### 行内公式

```
$W_{b}$
```

$W_{b}$

### 行间公式

```
$$
W_{b}
$$
```

$$
W_{b}
$$

