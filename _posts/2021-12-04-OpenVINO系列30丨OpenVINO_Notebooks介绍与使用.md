---
layout: post
title: OpenVINO系列30丨OpenVINO_Notebooks介绍与使用
tag: OpenVINO
---

## 一.概述

该项目包含一些可以直接运行的Jupyter notebooks，方便我们学习和使用openvino，通过这些提供的sample,我们可以清楚的了解openvino的工作原理，以及如何更轻松的在我们的深度学习程序中用openvino优化推理。

## 二.系统要求

运行OpenVINO Notebooks需要预装Python和Git，建议在python虚拟环境中使用。

![20211204211115](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204211115.png)

## 三.了解Notebooks

### 1.clone仓库到本地

    git clone https://github.com/openvinotoolkit/openvino_notebooks.git

![20211204212940](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204212940.png)

### 2.Notebooks功能分区

![Snipaste_2021-12-04_23-30-35](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/Snipaste_2021-12-04_23-30-35.png)

### 3.notebooks demo的功能说明

Notebook | 姓名 | 
- | - 
001-hello-world | 14行代码实现视觉分类检测应用 
002-openvino-api|Openvino python api介绍
003-hello-segmentation|基于Openvino的视觉语义分割应用
004-hello-detection|基于Openvino的文字识别应用
101-tensorflow-to-openvino|基于Tensorflow预训练模型，实现分类检测部署	
102-pytorch-onnx-to-openvino|基于Pytorch预训练模型，实现语义分割部署	
103-paddle-onnx-to-openvino|基于PadlePadle预训练模型，实现分类检测部署	
104-model-tools|Openvino模型的下载与评估	
105-language-quantize-bert|BERT预训练模型的优化与量化
201-vision-monodepth|单目深度检测应用实现	
202-vision-superresolution-image|图像超分辨率应用实现
202-vision-superresolution-video|视频超分辨率应用实现	
205-vision-background-removal|图像背景替换的应用实现	
206-vision-paddlegan-anime|利用paddleGAN的图片风格转换的应用实现
207-vision-paddlegan-superresolution|利用paddleGAN的图像超分辨率应用实现	
208-optical-character-recognition|文字识别应用实现
301-tensorflow-training-openvino|利用Tensorflow及OpenVINO 实现从模型训练到优化部署的完整流程	
301-tensorflow-training-openvino-pot|基于POT工具的模型量化	
302-pytorch-quantization-aware-training|基于NNCF工具的模型压缩	
402-pose-etimation-webcam|基于openvino人体姿态评估	

## 四.获取与启动Notebooks

### 1.创建虚拟环境

![20211204212903](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204212903.png)

### 2.更新pip、安装环境依赖

    python3 -m pip install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple
    cd openvino_notebooks
    pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

### 3.用ipykernel创建运行环境的专用内核,并指定名称

    python3 -m ipykernel install --user --name openvino_env

![20211204214005](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204214005.png)

### 4.启动单个Notebooks，比如201-vision-monodepth

    jupyter notebook notebooks/201-vision-monodepth/201-vision-monodepth.ipynb

![20211204214236](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204214236.png)

### 5.启动所有Notebooks

    jupyter lab notebooks

![20211204231027](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20211204231027.png)

## 四.tensorflow-to-openvino

    import time
    from pathlib import Path
    import cv2
    import matplotlib.pyplot as plt
    import numpy as np
    from IPython.display import Markdown
    from openvino.inference_engine import IECore

    # The paths of the source and converted models
    model_path = Path("model/v3-small_224_1.0_float.pb")
    ir_path = Path(model_path).with_suffix(".xml")

    # Construct the command for Model Optimizer
    mo_command = f"""mo 
                    --input_model "{model_path}" 
                    --input_shape "[1,224,224,3]" 
                    --mean_values="[127.5,127.5,127.5]"
                    --scale_values="[127.5]" 
                    --data_type FP16 
                    --output_dir "{model_path.parent}"
                    """
    mo_command = " ".join(mo_command.split())
    print("Model Optimizer command to convert TensorFlow to OpenVINO:")
    display(Markdown(f"`{mo_command}`"))

    # Run Model Optimizer if the IR model file does not exist
    if not ir_path.exists():
        print("Exporting TensorFlow model to IR... This may take a few minutes.")
        ! $mo_command
    else:
        print(f"IR model {ir_path} already exists.")

    ie = IECore()
    net = ie.read_network(model=ir_path, weights=ir_path.with_suffix(".bin"))
    exec_net = ie.load_network(network=net, device_name="CPU")

    input_key = list(exec_net.input_info)[0]
    output_key = list(exec_net.outputs.keys())[0]
    network_input_shape = exec_net.input_info[input_key].tensor_desc.dims

    # The MobileNet network expects images in RGB format
    image = cv2.cvtColor(cv2.imread(filename="data/coco.jpg"), code=cv2.COLOR_BGR2RGB)

    # Resize image to network input image shape
    resized_image = cv2.resize(src=image, dsize=(224, 224))

    # Transpose image to network input shape
    input_image = np.reshape(resized_image, network_input_shape) / 255
    input_image = np.expand_dims(np.transpose(resized_image, (2, 0, 1)), 0)
    plt.imshow(image);

    result = exec_net.infer(inputs={input_key: input_image})[output_key]
    result_index = np.argmax(result)

    # Convert the inference result to a class name.
    imagenet_classes = open("utils/imagenet_2012.txt").read().splitlines()

    # The model description states that for this model, class 0 is background,
    # so we add background at the beginning of imagenet_classes
    imagenet_classes = ['background'] + imagenet_classes

    imagenet_classes[result_index]

    num_images = 1000
    start = time.perf_counter()
    for _ in range(num_images):
        exec_net.infer(inputs={input_key: input_image})
    end = time.perf_counter()
    time_ir = end - start
    print(
        f"IR model in Inference Engine/CPU: {time_ir/num_images:.4f} "
        f"seconds per image, FPS: {num_images/time_ir:.2f}"
    )

## 五.tensorflow-training-openvino


