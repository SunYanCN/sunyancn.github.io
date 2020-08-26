---
title: Docker入手
categories: 项目
tags: Docker
abbrlink: 20925
date: 2018-11-30 21:25:59
---
![下载.png](https://i.loli.net/2018/11/30/5c013a3895a19.png)
## Docker 简介
Docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器。开发者在笔记本上编译测试通过的容器可以批量地在生产环境中部署，包括VMs（虚拟机）、 bare metal、OpenStack 集群和其他的基础应用平台。 
Docker通常用于如下场景：
- web应用的自动化打包和发布；
- 自动化测试和持续集成、发布；
- 在服务型环境中部署和调整数据库或其他的后台应用；
- 从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。

<!--more-->
### 安装Curl
使用 APT包管理工具安装 cURL：`sudo apt install -y curl`
### 安装 Docker
官方已经给出了适合 Linux 平台的自动安装脚本。因此想要安装 Docker，只需要运行下面的命令：
```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
在上面的命令中，我们添加了参数--mirror 以使用国内的安装包镜像。
## 添加Docker Hub镜像加速
### 创建daemon.json文件
创建配置文件，`sudo vi /etc/docker/daemon.json`，添加镜像服务地址。腾讯云镜像的配置如下：
```json
{
    "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
}
```
### 重新启动Docker
```shell
sudo systemctl daemon-reload
suod systemctl restart docker
```

## 测试
`docker version`

## Hello World
运行下面的命令，将 image 文件从仓库抓取到本地。
`docker pull library/hello-world`
上面代码中，docker image pull是抓取 image 文件的命令。library/hello-world是 image 文件在仓库里面的位置，其中library是 image 文件所在的组，hello-world是 image 文件的名字。抓取成功以后，就可以在本机看到这个 image 文件了。
```shell
docker images
#显示结果
REPOSITORY                      TAG                 IMAGE ID            CREATED             SIZE
docker.io/hello-world           latest              f2a91732366c        3 months ago        1.848 kB
```
现在，运行这个 image 文件。
```shell
docker run hello-world

#显示结果
Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```
输出这段提示以后，hello world就会停止运行，容器自动终止。有些容器不会自动终止，因为提供的是服务，比如Mysql镜像等。
## 常用命令
除过以上我们使用的Docker命令外，Docker还有一些其它常用的命令
1. 拉取docker镜像 `docker pull image_name`
2. 查看宿主机上的镜像，Docker镜像保存在/var/lib/docker目录下:`docker images`
3. 删除镜像:`docker rmi  docker.io/tomcat:7.0.77-jre7   或者  docker rmi b39c68b7af30`
4. 查看当前有哪些容器正在运行:`docker ps`
5. 查看所有容器`docker ps -a`
6. 启动、停止、重启容器命令：
```shell
docker start container_name/container_id
docker stop container_name/container_id
docker restart container_name/container_id
```
7. 动一个容器后，如果想进入到这个容器，可以使用attach命令：`docker attach container_name/container_id`
8. 删除容器的命令：`docker rm container_name/container_id`
9. 查看当前系统Docker信息:`docker info`
10. 从Docker hub上下载某个镜像:
```shell
docker pull centos:latest
```
执行docker pull centos会将Centos这个仓库下面的所有镜像下载到本地repository。
