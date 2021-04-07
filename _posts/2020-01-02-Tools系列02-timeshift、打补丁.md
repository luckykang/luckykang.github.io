---
layout: post
title: Tools系列02-timeshift、打补丁
tag: Tools
---


### 1.git patch

#### 生成补丁
    git init 
    git add .
    git status 

    # 修改完内容后,生成patch
    git diff > a.diff

#### 打补丁
    # 先检查是否报错
    git diff --check apply a.diff  

    # 如果没报错就可以打   
    git diff apply a.diff

### 2.timeshift

    sudo add-apt-repository -y ppa:teejee2008/ppa

    sudo  apt update

    sudo  apt install timeshift

### 3.ssh-server

    # 安装
    sudo apt install openssh-server

    # 查看安装的服务
    dpkg -l | grep ssh

    #确认ssh-server是否启动
    ps -e | grep ssh





