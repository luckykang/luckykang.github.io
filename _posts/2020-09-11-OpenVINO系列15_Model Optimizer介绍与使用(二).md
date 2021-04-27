---
layout: post
title: OpenVINO系列15_Model Optimizer介绍与使用(二)
tag: OpenVINO
---


### 1.模型优化的格式(以squeezenet1.0为例)

我们在做模型优化的时候，使用不同的数据和权重格式该怎么权衡呢？我们不能为了把权重压缩到较小的格式而不考虑模型的准确性，那将会引起量化错误。不同的设备支持的格式也不同，那么我们是如何做不同格式的模型优化呢？

- 1.下载模型

```
sudo -E ./downloader.py  --name squeezenet1.0 -o ~/Documents/
```

![20210427165331](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210427165331.png)

- 2.使用`model_optimizer`进行模型优化

执行下面命令，优化为FP32格式的IR文件

```
sudo python3 ./mo_caffe.py  --input_model /home/kang/Documents/public/squeezenet1.0/squeezenet1.0.caffemodel --output_dir  $model --model_name squeezenet1.0_FP32  --data_type FP32
```

![20210427170652](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210427170652.png)


如果我们想使用GPU来加速推理，可以优化为FP16的格式。

```
sudo python3 ./mo_caffe.py  --input_model /home/kang/Documents/public/squeezenet1.0/squeezenet1.0.caffemodel --output_dir  $model --model_name squeezenet1.0_FP16  --data_type FP16
```

现在，我们的路径下有了FP32和FP16两种格式可选。

![20210427171152](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210427171152.png)

### 