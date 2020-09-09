---
layout: post
title: face_detection检测人脸并做标记
tag: OpenVINO
---

***

#### 效果展示  


![out](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/out.png)

***  

### 代码demo

    import cv2 as cv

    # 加载模型
    net = cv.dnn.readNet('/home/kang/open_model_zoo-2019/model_downloader/Transportation/object_detection/face/pruned_mobilenet_reduced_ssd_shared_weights/dldt/face-detection-adas-0001.xml', '/home/kang/open_model_zoo-2019/model_downloader/Transportation/object_detection/face/pruned_mobilenet_reduced_ssd_shared_weights/dldt/face-detection-adas-0001.bin')

    # 指定目标设备CPU
    net.setPreferableTarget(cv.dnn.DNN_TARGET_CPU)

    # 加载图像
    frame = cv.imread('/home/kang/Desktop/0903171727.png')

    # 准备输入 blob 
    blob = cv.dnn.blobFromImage(frame, size=(672, 384), ddepth=cv.CV_8U)

    # 执行推理
    net.setInput(blob)
    out = net.forward()

    # 绘制检测到的脸
    for detection in out.reshape(-1, 7):

        confidence = float(detection[2])

        xmin = int(detection[3] * frame.shape[1])
        ymin = int(detection[4] * frame.shape[0])
        xmax = int(detection[5] * frame.shape[1])
        ymax = int(detection[6] * frame.shape[0])
        # BGR格式
        # if confidence > 0.5:
        # 为了识别更多的人脸，可以调节检测的置信度，使得程序对人脸的判定没那么严格，这里下调为0.2(原来是0.5)
        if confidence > 0.2:
            cv.rectangle(frame, (xmin, ymin), (xmax, ymax), color=(0, 255, 0))

    # 保存图像文件
    cv.imwrite('./out.png', frame)



<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io)
