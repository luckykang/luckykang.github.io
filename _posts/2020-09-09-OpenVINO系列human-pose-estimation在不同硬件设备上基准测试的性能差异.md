---
layout: post
title: OpenVINO系列human-pose-estimation在不同硬件设备上基准测试的性能差异
tag: OpenVINO
---


### 测试平台

- 测试系统：ubuntu 18.04LTS
- cpu型号：i5-6350HQ
- 核显型号：GT4e
- HDDL设备：4个VPU

### 一、在CPU上的测试

#### 1.使用FP32数据格式进行测试

![CPU-FP32](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/CPU-FP32.png)


#### 2.使用INT8数据格式进行测试


![CPU-INT8](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/CPU-INT8.png)

### 二、在GPU上的测试

#### 1.使用FP16数据格式进行测试

![GPU-FP16](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/GPU-FP16.png)

### 三、在HDDL上的测试

#### 1.使用FP32数据格式进行测试

![hddl_01](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/hddl_01.png)
![hddl_02](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/hddl_02.png)


### 结论

通过测试发现，human-pose-estimation在不同设备上运行的时候，CPU在int8的数据格式下吞吐量为23.64fps,FP32下的吞吐量为16.53fps,说明CPU在int8表现更优。

GPU在FP16的数据格式下吞吐量为60.83fps,要比CPU好不少。

HDDL在FP32的数据格式下吞吐量为40.19fps,介于两者之间。

<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)
