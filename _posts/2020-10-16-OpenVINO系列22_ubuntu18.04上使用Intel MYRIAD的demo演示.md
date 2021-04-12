---
layout: post
title: OpenVINO系列22_ubuntu18.04上使用Intel MYRIAD的demo演示
tag: OpenVINO
---

### 1.在ubuntu上配置usb驱动：
```
cd /opt/intel/openvino_2019.3.334/install_dependencies

./install_NCS_udev_rules.sh
```

![20210412133303](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412133303.png)


### 2.配置所有框架的Model Optimizer
```
cd /opt/intel/openvino_2019.3.334/deployment_tools/model_optimizer/install_prerequisites/

./install_prerequisites.sh
```

![20210412133344](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412133344.png)

### 3.测试
```
cd /opt/intel/openvino_2019.3.334/deployment_tools/demo

./demo_squeezenet_download_convert_run.sh -d MYRIAD
```
![20210412133405](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412133405.png)

![20210412133424](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210412133424.png)









