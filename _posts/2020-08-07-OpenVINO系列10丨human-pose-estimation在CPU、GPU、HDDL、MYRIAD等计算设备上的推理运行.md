---
layout: post
title: OpenVINO系列10丨human-pose-estimation在CPU、GPU、HDDL、MYRIAD等计算设备上的推理运行
tag: OpenVINO
---

***

#### 使用设备为NUC，配置如下图：

![0034](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/0034.png)

***  

### 一、前期工作：
1.安装openvino，我使用的版本为l_openvino_toolkit_p_2020.4.287

2.下载human-pose-estimation-0001

![01](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/01.png)

3.执行bulid

![02](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/02.png)

4.执行完build以后，会看到提示所有的demos都在~/omz_demos_build/intel64/Release/目录下

![03](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/03.png)

5.切换到路径/home/kang/omz_demos_build/human_pose_estimation_demo，编译demo

![04](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/04.png)

### 二、在CPU上进行推理：
可以看到cpu的使用率达到了94%，不得不说占用还是蛮高的
![13123](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/13123.png)


### 三、在GPU上进行推理：
1.安装驱动

切换到路径/opt/intel/openvino/install_dependencies，使用root权限执行脚本
install_NEO_OCL_driver.sh

![11-45-01](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/11-45-01.png)

出现下图内容，就表示安装成功了

![11-45-19](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/11-45-19.png)

2.进行推理运行

可以看到在使用GPU推理的时候，CPU的使用率下降了，大概为50%
![11-58-00](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/11-58-00.png)

### 四、在HDDL上进行推理：

1.安装驱动

切换到路径/opt/intel/openvino/deployment_tools/inference_engine/external/hddl，执行install_IVAD_VPU_dependencies.sh

![13-18-54](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/13-18-54.png)

安装完成后会在myd_vsc.conf中加载4个rules,因为我使用的设备是有4个Movidius。

![13-19-32](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/13-19-32.png)

2.在HDDL上推理运行

可以看到在使用HDDL推理的时候，CPU的使用率实际情况是波动会比较大，在8%到80%之间
起伏，并不稳定
![13-24-37](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/13-24-37.png)


### 五、在MYRIAD上进行推理：

1.安装MYRIAD驱动

切换到路径/opt/intel/openvino/install_dependencies,执行install_NCS_udev_rules.sh

2.推理运行

执行推理，CPU使用率稳定在54.9%左右

![14-10-54](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/14-10-54.png)




<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)

