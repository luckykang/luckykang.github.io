---
layout: post
title: Python系列01丨virtualenv和virtualenvwrapper安装与使用
tag: Python
---

## 一.安装

首先更新到最新的pip

    python3 -m pip install -U pip

![20211128153625](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128153625.png)

virtualenvwrapper，相较于使用virtualenv，好处就是把所有环境都放在同一目录下管理，以便更好的管理及切换。

    python3 -m pip install virtualenv  virtualenvwrapper

查看virtualenv版本

![20211128154401](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128154401.png)

安装virtualenvwrapper完成后，去/usr/local/bin目录下会看到virtualenvwrapper.sh的文件

![20211128153913](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128153913.png)

## 二.配置环境变量

运行脚本文件看到，我们还要配置一下`~/.bashrc`的文件。

![20211128154547](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128154547.png)

在喜欢的路径下创建文件夹，名称自己可以随意更改，相应的代码就要进行更改

    mkdir $HOME/.venv

查看python3安装路径

![20211128155705](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128155705.png)

执行命令，打开~/.zshrc

    vim  ~/.zshrc

在末尾添加，”:wq” 保存

    export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
    export WORKON_HOME=$HOME/.venv
    source /usr/local/bin/virtualenvwrapper.sh

如果成功会显示下图

    source ~/.zshrc

![20211128155551](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128155551.png)

## 三.常用操作

### 1.创建虚拟环境

创建虚拟环境名tf_test, 完成后自动进入环境

    mkvirtualenv tf_test

![20211128163208](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128163208.png)

### 2.激活环境

查看已有的虚拟环境,并激活

    workon 
    workon tf_test

![20211128163524](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128163524.png)

### 3.退出

    deactivate

### 4.删除环境

要退出该环境才能删除，否则会报错

rmvirtualenv  tonado_py3

![20211128164053](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128164053.png)

### 5.在Pycharm导入

![20211128165426](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211128165426.png)

