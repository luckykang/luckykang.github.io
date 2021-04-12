---
layout: post
title: OpenVINO系列22_ubuntu上使用Intel GPU 的demo演示
tag: OpenVINO
---

### 1.测试环境：

系统环境:ubuntu18.04LTS

openvino版本：2020 R1

### 2.安装步骤：

- 安装使用GPU插件和图形OpenCL驱动程序组件：

```
cd /opt/intel/openvino/install_dependencies
sudo -E su
./install_NEO_OCL_driver.sh
```

- 遇到报错： 
```
error processing package intel-igc-core
```
![20210412132552](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412132552.png)

- 解决办法：

可以通过apt在ubuntu中首先删除intel-igc-opencl,然后再删除intel-igc-core

```
apt remove intel-igc-opencl
apt remove intel-igc-core
```
![20210412132617](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412132617.png)

- 最后再执行脚本,执行成功

```
./install_NEO_OCL_driver.sh
```
![20210412132643](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412132643.png)

### 3.使用：

```
cd ~/inference_engine_samples_build/intel64/Release
./classification_sample_async -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/openvino_models/ir/public/squeezenet1.1/FP16/squeezenet1.1.xml -d GPU
```

![20210412132703](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412132703.png)








