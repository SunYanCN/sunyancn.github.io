---
title: Docker部署深度学习模型
tags: docker
mathjax: false
abbrlink: 1488
date: 2019-09-27 15:57:33
---
## Docker安装
参考链接：https://docs.docker.com/install/linux/docker-ce/centos/
运行到`sudo docker run hello-world`正常输出即可
<!--more-->
## Docker国内加速
**操作步骤**
- 编辑文件`/etc/docker/daemon.json`，没有就新建。加入以下项目：
```bash
{
    "registry-mirrors": ["https://registry.docker-cn.com","http://hub-mirror.c.163.com"]
}
```
- 重启docker daemon : `systemctl restart docker`

也可以写一个脚本
```bash
#!/bin/bash
cat>/etc/docker/daemon.json<<EOF
{
  "registry-mirrors": ["https://registry.docker-cn.com","http://hub-mirror.c.163.com"]
}
EOF
systemctl restart docker
```

## 拉取tensorflow serving

