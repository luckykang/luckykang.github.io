---
layout: post
title: 常用的的timeshift、打补丁、log
tag: 工具
---


### 1.git patch

#### 生成补丁

    git add .
    git status 
    # 修改完成后
    git diff > a.diff

#### 打补丁
    # 先检查是否报错
    git diff --check apply a.diff     
    git diff apply a.diff

### 2.timeshift

    sudo add-apt-repository -y ppa:teejee2008/ppa

    sudo  apt update

    sudo  apt install timeshift

### 3.log

    cd /var/log/esb-cli/   
    
    sudo zip -r xx.zip xx  

### 4.child

    sudo gedit /etc/apt/apt.conf

    Acquire::http::Proxy "http://c-prc.xx.com:913";


    sudo gedit /etc/environment

    export http_proxy='http://c-prc.xx.com:913/'
    export https_proxy='http://c-prc.xx.com:913/'


