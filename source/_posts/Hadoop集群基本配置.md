---
title: Hadoop集群基本配置
categories: 项目
tags: Hadoop
abbrlink: 8797
date: 2018-11-05 19:44:30
---
![CDAP.png](https://i.loli.net/2018/11/30/5c014444993e8.png)
<!--more-->
## 集群规划

| 集群节点分配 |                 |
| ------------ | --------------- |
| 主机名       | 主机IP          |
| master       | 192.168.100.100 |
| slave1       | 192.168.100.101 |
| slave2       | 192.168.100.102 |
| slave3       | 192.168.100.103 |

<!--more-->

| 软件版本 |                   |
| -------- | ----------------- |
| 软件名称 | 版本号            |
| Java     | 1.8.0_152         |
| CentOS   | CentOS-7-x64-1708 |
| Hadoop   | 2.7.5             |
| MySQL    | 5.7.20            |
| Hive     | 2.3.2             |

| 各软件安装路径 |                       |
| -------------- | --------------------- |
| Hadoop         | /opt/SoftWare/Hadoop/ |
| Java           | /opt/SoftWare/Java/   |
| Hive           | /opt/SoftWare/Hive    |
| MySQL          | /opt/SoftWare/MySQL   |

## 各主机基础软件安装及操作

由于使用的是最小化安装，因此这里需要安装不少软件才能进行后续操作。

### openssh安装，便于远程上传文件

```shell
[root@master ~]# sudo yum -y install openssh-clients openssh-server
```
安装完成后，可以使用下面命令进行测试：
`ssh localhost`
输入root账户的密码，如果可以正常登录，则说明SSH安装没有问题。测试正常后使用exit命令退出ssh。

### 同步时间工具

```shell
#安装ntpdate工具
[root@master ~]# yum -y install ntp ntpdate
#设置与网络时间同步
[root@master ~]# ntpdate cn.pool.ntp.org
#系统时间写入硬件时间
[root@master ~]# hwclock --systohc
```

### 文件上传(rz)下载(sz)工具

可以在Xshell工具中通过rz调出上传文件的窗口进行文件上传，也可以通过sz 文件名下载某一个文件。

```shell
[root@master ~]# yum -y install lrzsz
```

### 安装网络下载yum工具 wget

```shell
[root@test ~]# yum -y install wget
```

###  关闭防火墙

```shell
#查看防火墙开启状态
[root@test ~]# systemctl status firewalld
#关闭防火墙
[root@test ~]# systemctl stop firewalld
#禁止开机启动防火墙
[root@test ~]# systemctl disable firewalld
#开启防火墙
[root@test ~]# systemctl start firewalld
#设置开机启动防火墙
[root@test ~]# systemctl enable firewalld
#重启防火墙
[root@test ~]# systemctl restart firewalld
```

## 配置SSH免密码登录

四台主机均按照步骤2安装基础软件工具（这里不再过多叙述）

修改hosts文件，添加以下内容，四台主机均进行操作

```shell
[root@master ~]# vi /etc/hosts
#127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
#::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.100.100 master
192.168.100.101 slave1
192.168.100.102 slave2
192.168.100.103 slave3
```

配置SSH免密码登录

```shell
#每台机器先使用ssh执行以下，以在主目录产生一个.ssh 文件夹
[root@master ~]# ssh master
#然后输入no即可
#每台机器均进入~/.ssh 目录进行操作
[root@master ~]# cd ~/.ssh
#输入以下命令，一路回车，用以产生公钥和秘钥
[root@master .ssh]# ssh-keygen -t rsa -P ''
#出现以下信息说明生成成功
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:6YO1h1emM9gcWvv9OT6ftHxLnjP9u8p25x1o30oq3No root@master
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|                 |
|                 |
|         .       |
|        S o o    |
|       + O *  .  |
|      . B.X. o.+.|
|         +o=+=**%|
|          .oEo*^^|
+----[SHA256]-----+
#将每台机器上的id_rsa.pub公钥内容复制到authorized_keys文件中
[root@master .ssh]# cp id_rsa.pub authorized_keyscd
#将所有的authorized_keys文件进行合并（最简单的方法是将其余三台slave主机的文件内容追加到master主机上）
[root@slave1 .ssh]# cat ~/.ssh/authorized_keys | ssh root@master 'cat >> ~/.ssh/authorized_keys'
[root@slave2 .ssh]# cat ~/.ssh/authorized_keys | ssh root@master 'cat >> ~/.ssh/authorized_keys'
[root@slave3 .ssh]# cat ~/.ssh/authorized_keys | ssh root@master 'cat >> ~/.ssh/authorized_keys'
#查看master上的authorized_keys文件内容，类似如下即可
[root@master .ssh]# more authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5iw8+LlLxo0d77uaTChOKKJqfMHzp2jgzqV2hFAneFXqqWmr
Z4/FrMUPenmdss19bP4Up9G7PGbJu29yZDvkDwlmuqnVajYyDOsCl7PPXPWXMIlxMGUHgSXLnQQi6QnWp04vJKDs0EbiRTd0ZYCSQefzJcZ8jbQ7bLYt6jtil7FfUupTdHTeexKKd8Mq3K7YFZHumKvhzs6wWiM+n41jANS083ss3OYmAdO2cU0w1BhLVvJhdzd6fNG3RXVCXI2v0XxCUHiqI9Oewl2qPOfKzeyy09bJxo371Ezjmt8GMrkA/Ecepkvx12qwNzC9bSPLfbnPWVo2gIxe4mMaFqCFJ root@master
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3CkB3Jejavt+yFphsbciktWciJmbcUBOv3ZLPVLW18ZxXGZK
vG50EPXo/4By7P6IRW0wCa5YuckQEW+q6kmSatxqJ8e/K9a1mAk13N4f7V7M71Nn8IkujlF3gHYjKrmnEWpGJCy5YBURzywIQTRArlIac1xj2SeM6q+gTMV9WrAKJupIRHli+W0kHVaYHNdKl7KMUT4KVrSl+h4wFwAd7Tcyj7JIbUcCCL6o/v/LqGFwpcJfbfUsuKJJho+tImh41j7mSXR8kRbTSZkcq5KX+iANrANwOHZ58tV5KXmMQjuVq7aJ985C16hHssB6zq/zjAxpxAyQIeE8Incc8U8ix root@slave1
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC//uaMbzbkYqxdgZJSdq+gdQYldzMQ7D3SxsUaNO5oVnVOszw
+mbNnmL8vp1EUUehabQHPCAvCmLKUPXzfcxlyJEF/pnY77u4ySwsRVEpHvsDZbrclgCOrS6hW00sSx303KHLOgXT70LfrmnohfUhvTxajzLXT+C8f5ZfTZ8meKD73HKl16jRwZQ8YhW9GUyuCkgQTGtKtTKPsRUd9LpAc/7/u8xvvvNvTYPxgyTJcUMzGSOHh8J3upI54ykY0FgBkjs1fCUaDalxAgsHw9B1iyx706WbcT6ymiQVMKGnnnM6k2KPvUvfDswVfUSG+4ZsYSRHRTgWuiBbHoIr7DVd root@slave2
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDTzTTdGRTd1zts3m7uKobcgRom4lVyF9EdNOdbBWMucYjbCs
BgP1ideDDQed2TyBj3Szz3Yx6h1L4McGmATY/D9qRLml26VW/x0Tod8JYkqOQpQL9knLW2cwITnhLzq5VDugOix06r/uweP3Zed9CO7ld3jUxJJNZCYpsNz+eUKq9SWM5+ehUu9pfZZu9zUk7Q01js3uCHzu1AhsajgNzgB4+YLLccdHBfxGg4ix5wuaF82PlEEh70hTdfRkq8pqPMZ+FIQtTgfD5XllKTcnPItUY23hc7Umx4I3ujOd810vzffWYK07cOtv1r7LEcYtYqbZ6zIvII+M775iRkzQX root@slave3

#将master上的authorized_keys文件分发到其他主机上
[root@master .ssh]# scp ~/.ssh/authorized_keys root@slave1:~/.ssh/
[root@master .ssh]# scp ~/.ssh/authorized_keys root@slave2:~/.ssh/
[root@master .ssh]# scp ~/.ssh/authorized_keys root@slave3:~/.ssh/
#每台机器之间进行ssh免密码登录操作，包括自己与自己
[root@master ~]# ssh master
[root@master ~]# ssh slave1
[root@slave1 ~]# ssh master
[root@master ~]# ssh slave2
[root@slave2 ~]# ssh master
[root@master ~]# ssh slave3
[root@slave3 ~]# ssh master
[root@master ~]# ssh slave1
[root@slave1 ~]# ssh slave1
[root@slave1 ~]# ssh slave2
[root@slave2 ~]# ssh slave1
[root@slave1 ~]# ssh slave3
[root@slave3 ~]# ssh slave1
[root@slave1 ~]# ssh slave2
[root@slave2 ~]# ssh slave2
[root@slave2 ~]# ssh slave3
[root@slave3 ~]# ssh slave2
[root@slave2 ~]# ssh slave3
[root@slave3 ~]# ssh slave2
[root@slave2 ~]# ssh slave3
[root@slave3 ~]# ssh slave3
```

## 安装配置Java环境并测试

### 下载jdk
使用yum来安装1.8版本OpenJDK：
```shell
sudo yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel
```
安装完成后，输入 java 和 javac 命令，如果能输出对应的命令帮助，则表明jdk已正确安装。
### 配置 JAVA 环境变量
执行命令:编辑 ~/.bashrc，在结尾追加：
```shell
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
```
保存文件后执行下面命令使 JAVA_HOME 环境变量生效:
```shell
source ~/.bashrc
```
为了检测系统中 JAVA 环境是否已经正确配置并生效，可以分别执行下面命令:
```shell
java -version
$JAVA_HOME/bin/java -version
```
若两条命令输出的结果一致，且都为我们前面安装的openjdk-1.8.0的版本，则表明JDK环境已经正确安装并配置。
## 安装配置Hadoop并配置

### 下载Hadoop到本地
```shell
wget https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/hadoop-2.7.6/hadoop-2.7.6.tar.gz
```

### 上传至master节点

```shell
#上传
[root@master ~]# cd /opt/SoftWare/Hadoop
[root@master Hadoop]# rz
#解压
[root@master Hadoop]# tar -zxvf hadoop-2.7.5.tar.gz
```

### 创建目录

```shell
#进入hadoop-2.7.5主目录
[root@master Hadoop]# cd hadoop-2.7.5
#创建以下目录，以备后用
[root@master hadoop-2.7.5]# mkdir tmp
[root@master hadoop-2.7.5]# mkdir logs
[root@master hadoop-2.7.5]# mkdir -p hdfs/name
[root@master hadoop-2.7.5]# mkdir -p hdfs/dat
```

## 修改配置

### 修改hadoop-env.sh文件

```bash
[root@master hadoop-2.7.5]# vi etc/hadoop/hadoop-env.sh
#修改JAVA_HOME为以下内容，否则容易出现Hadoop无法启动问题
export JAVA_HOME=/opt/SoftWare/Java/jdk1.8.0_152
```

### 修改yarn-env.sh文件

```bash
[root@master hadoop-2.7.5]# vi etc/hadoop/yarn-env.sh 
#修改JAVA_HOME为以下内容
export JAVA_HOME=/opt/SoftWare/Java/jdk1.8.0_152
```

## 编辑slaves文件

```bash
#该文件用于记录本集群有哪些数据节点
[root@master hadoop-2.7.5]# vi etc/hadoop/slaves 
#删除该文件中原来的内容，添加以下内容
slave1
slave2
slave3
```

echo $JAVA_HOME

## 修改core-site.xml文件

```bash
[root@master hadoop-2.7.5]# vi etc/hadoop/core-site.xml
```

该文件为Hadoop的核心配置文件，非常重要

```xml
<!--在<configuration></configuration>中间添加一下内容-->
<property>
    <name>fs.defaultFS</name><!--定义Hadoop Master的URI和端口-->
    <value>hdfs://master:9000</value>
</property>
<property>
    <name>hadoop.tmp.dir</name><!--hadoop的临时存储目录-->
    <value>file:/opt/SoftWare/Hadoop/hadoop-2.7.5/tmp</value>
</property>
<property>
    <name>io.file.buffer.size</name><!--用作序列化文件处理时读写buffer的大小-->
    <value>131702</value>
</property>
```

