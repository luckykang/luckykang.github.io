---
layout: post
title: OpenVINO系列03丨windows上运行text-detection-0002
tag: OpenVINO
---

### 一.下载text-detection-0002

#### 注意：第一次运行需要从github上克隆 open_model_zoo 

    cd C:\Users\<user_name>

    git clone https://github.com/opencv/open_model_zoo.git

    cd C:\Users\<user_name>\open_model_zoo\tools\downloader

查看所有模型：

    python downloader.py --print_all

下载模型：

    python downloader.py --name text-detection-0002

下载路径为：

    C:\Users\<user_name>\open_model_zoo\model_downloader\Retail\object_detection\text\pixel_link_mobilenet_v2\0001\text-detection-0002.xml
    C:\Users\<user_name>\open_model_zoo\model_downloader\Retail\object_detection\text\pixel_link_mobilenet_v2\0001\text-detection-0002.bin

### 二.编译

    "C:\Program Files (x86)\IntelSWTools\openvino\deployment_tools\inference_engine\samples\build_samples_msvc2017.bat" 

### 三.执行推理

    cd C:\Users\<user_name>\Documents\Intel\OpenVINO\inference_engine_samples_build_2017\intel64\Release

    text_detection_demo.exe -i "C:\Program Files (x86)\IntelSWTools\openvino\deployment_tools\demo\Image_20190423172029.jpg" -m_td "C:\Users\<user_name>\open_model_zoo\model_downloader\Retail\object_detection\text\pixel_link_mobilenet_v2\0001\text-detection-0002.xml"

得到结果为：

![20210222150846](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210222150846.png)



