---
layout: post
title: OpenVINO系列04_Ubuntu上演示human-pose-estimation-0001
tag: OpenVINO
---

### 1.下载模型

进入open_model_zoo路径

    cd /opt/intel/openvino/deployment_tools/open_model_zoo/tools/downloader

查看所有模型命令：

    ./downloader.py --print_all

在模型列表中找到要下载的模型并下载：

    ./downloader.py --name human-pose-estimation-0001

记录xml文件下载路径：

    /opt/intel/openvino_2019.3.334/deployment_tools/open_model_zoo/tools/downloader/intel/human-pose-estimation-0001/FP32/human-pose-estimation-0001.xml

### 2.编译
 
执行下列命令

    cd /opt/intel/openvino/deployment_tools/inference_engine/demos

    ./build_demos.sh

执行完后，会在home目录生成下面文件目录：

    /home/kang/omz_demos_build/intel64/Release

进入`multichannel_demo`，执行make

    cd  ~/omz_demos_build/human_pose_estimation_demo

    make -j4

#### 注意：也可以直接在omz_demos_build目录中执行编译，则对文件夹下所有模型生效

### 3.运行

    cd ~/omz_demos_build/intel64/Release

    ./human_pose_estimation_demo -m  /opt/intel/openvino_2019.3.334/deployment_tools/open_model_zoo/tools/downloader/intel/human-pose-estimation-0001/FP32/human-pose-estimation-0001.xml  -i ~/Downloads/head-pose-face-detection-female.mp4  -d CPU

### 4.得到结果和图像信息

![20210222181423](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210222181423.png)





