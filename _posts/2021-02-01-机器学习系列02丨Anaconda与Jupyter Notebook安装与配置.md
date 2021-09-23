---
layout: post
title: 机器学习系列02丨Anaconda与Jupyter Notebook安装与配置
tag: 机器学习
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

![20210709140359](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210709140359.png)


### 4.在anaconda中新建虚拟环境

    conda update conda

    conda create -n AI_01

    conda env list

### 5.库的安装

新建`requirenments.txt`文件，复制下面的代码

    matplotlib
    numpy
    pandas
    tables
    jupyter


然后在终端执行  

    pip install -r requirements.txt

### 6.安装主题

#### 6.1 Install with pip

    # install jupyterthemes
    pip install jupyterthemes

    # upgrade to latest version
    pip install --upgrade jupyterthemes

#### 6.2 Install with conda

    # install jupyterthemes
    conda install -c conda-forge jupyterthemes

    # update to latest version
    conda update jupyterthemes

#### 6.3 查看和设置主题字体

- 主题参数

        # 查看列表
        jt -l      

        # 选择主题
        jt -t  onedork    

        # 恢复默认主题
        jt -r 

- 字体参数

        Code Font：-f

        Code Font-Size：-fs

        Notebook Font Size:-nfs

        Text/MD Cell Fontsize:-tfs

        Pandas DF Fontsize:-dfs

        Output Area Fontsize:-ofs

- 恢复默认设置

        jt -dfonts

- 常用设置

        jt -t onedork -f fira -fs 15 -altp -tfs 11 -nfs 11 -dfs 9 -ofs 11 -cellw 88% -T

[# 参考github #  https://github.com/dunovank/jupyter-themes/tree/b12d6c8843b5ff6fa13097d542588b055060ffb7](https://github.com/dunovank/jupyter-themes/tree/b12d6c8843b5ff6fa13097d542588b055060ffb7)

###### 查看效果

![20210722113707](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210722113707.png)


### 7.jupyter快捷键

- 两种模式通用快捷键
    - Shift+Enter,执行本单元代码，并跳转到下一单元
    - Ctrl+Enter,执行本单元代码，留在本单元

- 命令模式：按ESC进入

    - Y , cell切换到Code模模式
    - M ，cell切换到Markdown模式
    - A ，在当前cell上面添加cell
    - B , 在当前cell下面添加cell

- 编辑模式：按Enter进入

    - 补全代码：变量、方法后跟Tab键
    - 为一行或多行代码添加/取消注释： Ctrl+/

- 其他

    - 回退： Ctrl+Z
    - 重做： Ctrl+Y
    - 删除当前cell:  双击D
    - 快速跳转首个cell: Ctrl+Home
    - 快速跳转到最后一个cell: Ctrl+End


