---
layout: post
title: Tools系列08-Dockerfile的构建
tag: Tools
---

### 一.镜像原理

![20210828203147](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210828203147.png)

#### 1. Docker镜像的本质是什么？

是一个分层文件系统。
   
#### 2. Docker中的一个centos镜像只有200多M，而一个centos系统iso文件好几个G？

Centos的iso镜像文件包括bootfs和rootfs，而docker的centos镜像复用操作系统的bootfs,只有rootfs和其他镜像层。

#### 3. Docker中的一个tomcat镜像为什么有500M,而一个tomcat安装包只有70多M？

由于docker中镜像是分层的，tomcat虽然只有70多M，但它需要依赖父镜像和基础镜像，所以整个对外暴露的tomcat镜像大小500多M。

### 二.容器转为镜像

    # 把容器转换为镜像
    docker commit 容器id 镜像名称：版本号

    # 把镜像保存为压缩文件
    docker save -o 压缩文件名称 镜像名称：版本号

    # 压缩文件还原为镜像
    docker load -i 压缩文件名称

![20210828211017](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210828211017.png)

### 三.Dockerfile

#### 1.Dockerfile概念

![20210828211133](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210828211133.png)

#### 2.Dockerfile关键字

![20210828211501](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210828211501.png)
![20210828211549](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210828211549.png)

