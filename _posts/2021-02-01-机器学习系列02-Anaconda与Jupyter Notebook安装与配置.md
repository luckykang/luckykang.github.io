---
layout: post
title: 机器学习系列02-Anaconda与Jupyter Notebook安装与配置
tag: Tools
---

### 1.配置环境变量

![20210708165252](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210708165252.png)

### 2.配置国内源和PROXY

    channels:
    - defaults
    show_channel_urls: true
    default_channels:
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
    - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
    custom_channels:
    conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
    
    proxy_servers:
    http: http://c-prc.xxx.com:913 
    https: https://c-prc.xxx.com:913

### 3.安装 jupyter_contrib_nbextensions

    python -m pip install  jupyter_contrib_nbextensions

    jupyter contrib nbextension install --user --skip-running-check


![20210708180732](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210708180732.png)

### 4.在anaconda中新建虚拟环境

    conda update conda

    conda create -n AI_01

    conda env list

### 5.库的安装

新建`requirenments.txt`文件，复制下面的代码

    matplotlib==2.2.2
    numpy==1.14.2
    pandas==0.20.3
    tables==3.4.2
    jupyter==1.0.0


然后在终端执行  

    pip install -r requirements.txt
