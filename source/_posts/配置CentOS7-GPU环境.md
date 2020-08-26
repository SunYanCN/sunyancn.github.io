---
title: 配置CentOS7 GPU环境
categories: 项目
tags: GPU
abbrlink: 47363
date: 2018-11-07 17:57:51
---
![](https://i.loli.net/2018/11/07/5be2c0ba79f37.png)
## 基本配置
1. 先装wget
```shell
yum -y install wget
```
2. 新建自己的文件夹
```shell
mkdir sunyan
```
<!--more-->
## 安装Anoconda
1. 下载Anoconda。注意最新Anoconda的是3.7版本的，但是tensorflow还没有更新到3.7，这里我们安装2018年5月发布的python 3.6版本。
```shell
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.2.0-Linux-x86_64.sh
```

2. 安装bzip2
```shell
yum install -y bzip2
```
3. 安装Anoconda，一路yes即可。
```shell
 bash Anaconda3-5.2.0-Linux-x86_64.sh
 ```

4. 使能配置
```shell
source .bashrc
```
5. 输入Python，查看是否配置正确
![](https://i.loli.net/2018/11/07/5be2bf612e76c.png)

## Conda安装TesnorFlow GPU版本
1. 配置国内conda源。由于cudnn和cuda很大，conda又在国外，容易导致下载中断，这里我们配置国内中科大的镜像。
```shell
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
```
2. 安装
```shell
conda install tensorflow-gpu
```
我的cuda版本是9.0，cudnn版本是7.1.2，tensorflow-gpu版本是1.9.0。
![](https://i.loli.net/2018/11/07/5be2bf613b4fe.png)
![](https://i.loli.net/2018/11/07/5be2bf6139235.png)

## 安装NVIDIA驱动
1. 安装gcc
```shell
yum -y install gcc-c++
```

2. 安装Open JDK
```shell
yum  install  java-1.8.0-openjdk   java-1.8.0-openjdk-devel
```
3. 检测显卡驱动及型号
```shell
sudo rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
sudo rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
sudo yum install nvidia-detect
```
![](https://i.loli.net/2018/11/07/5be2bf613a072.png)
这里显示的是Tesla P4，410.66 NVIDIA driver

4. 去[官网](https://www.nvidia.cn/Download/index.aspx?lang=cn)下载对应的驱动
```shell
wget http://cn.download.nvidia.com/tesla/384.145/NVIDIA-Linux-x86_64-384.145.run
```

5. 屏蔽默认带有的nouveau,使用su命令切换到root用户下,然后修改/etc/modprobe.d/blacklist.conf 文件，如果系统没有该文件需要新建一个。
```shell
su root
echo -e "blacklist nouveau\noptions nouveau modeset=0" > /etc/modprobe.d/blacklist.conf
```
然后查看一下文件内容：
```shell
cat /etc/modprobe.d/blacklist.conf
```
![](https://i.loli.net/2018/11/07/5be2bf612d424.png)

6. 重建initramfs image
```shell
mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak
dracut /boot/initramfs-$(uname -r).img $(uname -r)
```

7. 修改运行级别为文本模式

```shell
systemctl set-default multi-user.target
```

8. 重新启动, 使用root用户登陆,这里需要等待一下，约1分钟。
```shell
sudo iptables stop
reboot
```

9. 查看nouveau是否已经禁用,如果没有显示相关的内容，说明已禁用。
```shell
ls mod | grep nouveau
```
10. 安装kenel-devel
```shell
wget ftp://ftp.riken.jp/Linux/cern/centos/7/updates/x86_64/Packages/kernel-devel-3.10.0-693.17.1.el7.x86_64.rpm
yum install kernel-devel-3.10.0-693.17.1.el7.x86_64.rpm 
```
10. 安装NVIDIA驱动，一路Enter即可
```shell
chmod +x NVIDIA-Linux-x86_64-384.145.run
sh NVIDIA-Linux-x86_64-384.145.run
```
## 测试
1. nvidia-smi测试和TensorFlow测试,显示GPU信息即可。报警告没有关系，是TF本身的警告，也可以通过`pip install h5py==2.8.0rc1`来解决。
```shell
nvidia-smi
python3
import tensorflow as tf
sess = tf.Session()
```
![](https://i.loli.net/2018/11/07/5be2bf617cf6d.png)
![](https://i.loli.net/2018/11/07/5be2bf619a6bc.png)

2. 更换PyPi为国内源
```shell
pip install pqi
pqi use tuna
pqi show
```

3. 更换conda源为国内源
```shell
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/msys2/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/menpo/
conda config --set show_channel_urls yes
```



