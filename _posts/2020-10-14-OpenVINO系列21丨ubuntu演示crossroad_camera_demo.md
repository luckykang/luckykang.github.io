---
layout: post
title: OpenVINO系列21丨ubuntu演示crossroad_camera_demo
tag: OpenVINO
---

### 1.测试环境：
ubuntu版本：18.04.1LTS

openvino版本：2020.1.023

模型文档链接：(https://docs.openvinotoolkit.org/latest/_demos_crossroad_camera_demo_README.html)[]


### 2.下载模型
进入open_model_zoo路径

```
cd /home/kang/open_model_zoo/tools/downloader
```

在模型列表中找到要下载的模型并下载：

```
./downloader.py --name person-vehicle-bike-detection-crossroad-0078
```

记录xml文件下载路径：

```
/home/kang/open_model_zoo/tools/downloader/intel/person-vehicle-bike-detection-crossroad-0078/FP32/person-vehicle-bike-detection-crossroad-0078.xml
```

### 3.编译
执行下列命令

```
cd /opt/intel/openvino/deployment_tools/inference_engine/demos

./build_demos.sh
```
![20210412113753](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412113753.png)

进入`crossroad_camera_demo`路径，执行`make`

```
cd  ~/omz_demos_build/crossroad_camera_demo
make -j4
```
![20210412113827](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412113827.png)

### 4.运行

```
cd ~/omz_demos_build/intel64/Release

./crossroad_camera_demo -m /home/kang/open_model_zoo/tools/downloader/intel/person-vehicle-bike-detection-crossroad-0078/FP32/person-vehicle-bike-detection-crossroad-0078.xml -d CPU -i /home/kang/Downloads/test_data/pedestrian.png
```

得到结果和图像信息。

![20210412131555](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412131555.png)





