---
title: Hexo Customization
categories: 工具
tags: Hexo
abbrlink: 64470
date: 2018-10-28 13:09:07
---
### bilibili插件
[bilibili](https://github.com/Z4Tech/hexo-tag-bilibili)
### 颜文字
[颜文字](http://www.yanwenzi.com/changyong/)
### 表情
[表情](https://www.emojicopy.com/)
### 下载按钮
```[shell]
{% btn https://www.baidu.com, 点击下载百度, download fa-lg fa-fw %}
```
{% btn https://www.baidu.com, 点击下载百度, download fa-lg fa-fw %}
### 引用
```[shell]
{% cq %}
人生乃是一面镜子，
从镜子里认识自己，
我要称之为头等大事，
也只是我们追求的目的！
{% endcq %}
```
{% cq %}
人生乃是一面镜子，
从镜子里认识自己，
我要称之为头等大事，
也只是我们追求的目的！
{% endcq %}
<!--more-->
### 代码块

```[Python] [代码] 
代码
```
### 图标
https://fontawesome.com/v4.7.0/
```shell
- <i class="fa fa-pencil"></i> 铅笔
- <i class="fa fa-cloud-upload"></i> 上传
- <i class="fa fa-download"></i> 下载
```
- <i class="fa fa-pencil"></i> 铅笔
- <i class="fa fa-cloud-upload"></i> 上传
- <i class="fa fa-download"></i> 下载

### 音乐
首先在站点文件夹根目录安装插件：
```shell
npm install hexo-tag-aplayer --save
```
然后文章中的写法：
```markdown
{% aplayer "歌曲名" "歌手名" "https://什么什么什么.mp3" "https://封面图.jpg" "lrc:https://歌词.lrc" %}
```
另外可以支持歌单：
```markdown
{% aplayerlist %}
{
    "autoplay": false,
    "showlrc": 3,
    "mutex": true,
    "music": [
        {
            "title": "歌曲名",
            "author": "歌手名",
            "url": "https://什么什么什么.mp3",
            "pic": "https://封面图.jpg",
            "lrc": "https://歌词.lrc"
        },
        {
            "title": "歌曲名",
            "author": "歌手名",
            "url": "https://什么什么什么.mp3",
            "pic": "https://封面图.jpg",
            "lrc": "https://歌词.lrc"
        }
    ]
}
{% endaplayerlist %}
```
### 视频
首先在站点文件夹根目录安装插件：
```shell
npm install hexo-tag-dplayer --save
```
然后文章中的写法：
```markdown
{% dplayer "url=https://什么什么什么.mp4" "https://封面图.jpg" "api=https://api.prprpr.me/dplayer/" "id=" "loop=false" %}
```

### Hexo 中 MathJax 的静态显示（svg）
[Hexo 中 MathJax 的静态显示（svg）](https://reuixiy.github.io/technology/computer/computer-aided-art/2018/05/16/hexo-mathjax-svg.html)

### Reference
[打造个性超赞博客Hexo+NexT+GitHubPages的超深度优化](https://reuixiy.github.io/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html)
