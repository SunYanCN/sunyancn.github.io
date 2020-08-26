---
title: 日常bug
tags: bug
mathjax: false
abbrlink: 52747
date: 2019-11-14 20:32:08
---
1. `TensorFlow报错FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated`,解决方案：`pip install numpy==1.16.0`
2. 国内镜像ustc稳定性大于tuna,配置`.bashrc`如下:
```bash
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://mirrors.ustc.edu.cn/anaconda/cloud/menpo/
  - https://mirrors.ustc.edu.cn/anaconda/cloud/bioconda/
  - https://mirrors.ustc.edu.cn/anaconda/cloud/msys2/
  - https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
  - defaults
show_channel_urls: true
```
3. 删除虚拟环境：`conda remove -n your_env_name --all`
4. nohup查看运行的后台进程`jobs -l`
5. Anaconda导出环境/从外部安装环境,导出已有环境：`conda env export > environment.yaml`,当我们想再次创建该环境，或根据别人提供的.yaml文件复现环境时，可以：`conda env create -f environment.yaml`
6. .tar.bz2文件解压命令,从网络上下载到的源码包,最常见的是.tar.gz 包,还有一部分是.tar.bz2包
.tar.gz格式解压为`tar   -zxvf   xx.tar.gz`
.tar.bz2格式解压为`tar   -jxvf    xx.tar.bz2`
7. Python忽略警告信息:
```python
import warnings
warnings.filterwarnings("ignore")
```
8. python命令生成requestment.txt文件,`pip freeze > requirements.txt`,安装：`pip install -r requirements.txt`

9.  run `nvidia-smi` ，遇见`Failed to initialize NVML: Driver/library version mismatch`的错误。参考下面的解决方案：

   ```bash
   I had reinstalled nvidia driver: run these commands in root mode:
   1. systemctl isolate multi-user.target
   2. modprobe -r nvidia-drm
   3. Reinstall Nvidia driver: chmod +x NVIDIA-Linux-x86_64–410.57.run
   4. systemctl start graphical.target
   and finally check nvidia-smi
   ```

   