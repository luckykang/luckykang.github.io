---
layout: post
title: OpenVINO系列05丨windows上演示multi-channel-face-detection-demo
tag: OpenVINO
---

### 1.下载模型

face-detection-retail-0004

human-pose-estimation-0001

### 2.构建应用

    "C:\Program Files (x86)\IntelSWTools\openvino\deployment_tools\inference_engine\samples\build_samples_msvc2017.bat" 

### 3.设置环境变量

    "C:\Program Files (x86)\IntelSWTools\openvino\bin\setupvars.bat"

### 4.切换路径

#### 注意：<user_name>为相应的用户名,<path>为文件路径

    cd C:\Users\<user_name>\Documents\Intel\OpenVINO\inference_engine_samples_build_2017\intel64\Release

### 5.演示预训练的人脸模型

    multi-channel-face-detection-demo.exe  -m "C:\Users\<user_name>\open_model_zoo\model_downloader\Retail\object_detection\face\sqnet1.0modif-ssd\0004\dldt\face-detection-retail-0004.xml"  -l "C:\Users\<user_name>\Documents\Intel\OpenVINO\inference_engine_samples_build_2017\intel64\Release\cpu_extension.dll" -d HETERO:FPGA,CPU  -nc 1

#### 报错：

![20210303135257](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210303135257.png)

#### 分析以及解决办法：

报错显示缺少插件`dliaPlgin.dll`，在对应路径中查找没有发现这个文件，我重新build了应用，显示成功但发现还是没找到这个文件，开始怀疑是参数的问题，也就是指定的可选参数 `-d HETERO:FPGA,CPU` 改为 `-d CPU`即可。

正确命令为：

    multi-channel-face-detection-demo.exe  -m "C:\Users\<user_name>\open_model_zoo\model_downloader\Retail\object_detection\face\sqnet1.0modif-ssd\0004\dldt\face-detection-retail-0004.xml"  -l "C:\Users\<user_name>\Documents\Intel\OpenVINO\inference_engine_samples_build_2017\intel64\Release\cpu_extension.dll" -d CPU -nc 1

### 6.演示两个视频文件

    multi-channel-face-detection-demo.exe  -m "C:\Users\<user_name>\open_model_zoo\model_downloader\Retail\object_detection\face\sqnet1.0modif-ssd\0004\dldt\face-detection-retail-0004.xml"  -l "C:\Users\<user_name>\Documents\Intel\OpenVINO\inference_engine_samples_build_2017\intel64\Release\cpu_extension.dll" -d CPU -i <path>\a.mp4  <path>\b.mp4




