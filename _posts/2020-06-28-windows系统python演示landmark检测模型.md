---
layout: post
title: windows系统python演示landmark检测
tag: OpenVINO
---

***

#### 效果展示  

![](/images//post//2020-06//2020-06-28.png)  

***  

## 一、 准备流程：

### 1. 在python环境中加载openvino
打开openvino安装目录如：
C:\Intel\openvino\python\python3.6

把目录下的openvino文件夹复制到

系统的python环境安装目录下如：  C:\Python36\Lib\site-packages

### 2. 编译
C:\Intel\openvino\deployment_tools\inference_engine\samples 路径下执行：

    build_samples_msvc2017.bat

执行完后在

C:\Users\kang\Documents\Intel\OpenVINO 目录

可以看到生成的

inference_engine_samples_build_2017 文件目录

在build目录中也可以找到cpu_extension：

    cpu_extension = "C:\Users\kang\Documents\Intel\OpenVINO\inference_engine_samples_build_2017\intel64\Release\cpu_extension.dll"

### 3. 下载模型，记录路径 
face-detection-adas-0001

landmarks-regression-retail-0009

记录xml地址

    model_xml = ""   
    model_bin = ""

## 二、 参数说明

### 1. 人脸检测
- 基于MobileNet v1版本
- 输入格式：[1x3x384x672] = BCHW
- 输出格式：[1，1，N，7] = [image_id, label, conf, x_min, y_min, x_max, y_max]

### 2. landmark提取
- landmark提取 - 基于卷积神经网络，提取5个点
- 输入 [1x3x48x48] = BCHW
- 输出 [1X10X1X1] = 五个点坐标(x0,y0,x1,y1...x4,y4)

### 3. python版本的api介绍
- 同步调用，执行输入
- Im_exec_net.infer(inputs={"0":face_roi})

#### 4. 获取输出
- landmark_res = Im_exec_net.request[0].outputs[Im_output_blob]
- landmark_res = np.reshape(landmark_res,(5,2))


## 三、 附录代码：

    import sys
    import cv2
    import numpy as np
    import time
    import logging as log
    from openvino.inference_engine import IENetwork, IEPlugin
    model_xml = "C:/Users/kang/Downloads/open_model_zoo-2019/model_downloader/Transportation/object_detection/face/pruned_mobilenet_reduced_ssd_shared_weights/dldt/face-detection-adas-0001.xml"
    model_bin = "C:/Users/kang/Downloads/open_model_zoo-2019/model_downloader/Transportation/object_detection/face/pruned_mobilenet_reduced_ssd_shared_weights/dldt/face-detection-adas-0001.bin"
    plugin_dir = "C:/Intel/openvino/deployment_tools/inference_engine/bin/intel64/Release"
    cpu_extension = "C:/Users/kang/Documents/Intel/OpenVINO/inference_engine_samples_build_2017/intel64/Release/cpu_extension.dll"

    landmark_xml = "C:/Users/kang/Downloads/open_model_zoo-2019/model_downloader/Retail/object_attributes/landmarks_regression/0009/dldt/landmarks-regression-retail-0009.xml"
    landmark_bin = "C:/Users/kang/Downloads/open_model_zoo-2019/model_downloader/Retail/object_attributes/landmarks_regression/0009/dldt/landmarks-regression-retail-0009.bin"


    def face_landmark_demo():
        log.basicConfig(format="[ %(levelname)s ] %(message)s",
                        level=log.INFO,
                        stream=sys.stdout)
        # Plugin initialization for specified device and load extensions library if specified
        log.info("Initializing plugin for {} device...".format("CPU"))
        plugin = IEPlugin(device="CPU", plugin_dirs=plugin_dir)
        plugin.add_cpu_extension(cpu_extension)

        # lut
        lut = []
        lut.append((0, 0, 255))
        lut.append((255, 0, 0))
        lut.append((0, 255, 0))
        lut.append((0, 255, 255))
        lut.append((255, 0, 255))

        # Read IR
        log.info("Reading IR...")
        net = IENetwork(model=model_xml, weights=model_bin)
        landmark_net = IENetwork(model=landmark_xml, weights=landmark_bin)

        if plugin.device == "CPU":
            supported_layers = plugin.get_supported_layers(net)
            not_supported_layers = [
                l for l in net.layers.keys() if l not in supported_layers
            ]
            if len(not_supported_layers) != 0:
                log.error(
                    "Following layers are not supported by the plugin for specified device {}:\n {}"
                    .format(plugin.device, ', '.join(not_supported_layers)))
                log.error(
                    "Please try to specify cpu extensions library path in demo's command line parameters using -l "
                    "or --cpu_extension command line argument")
                sys.exit(1)
        assert len(
            net.inputs.keys()) == 1, "Demo supports only single input topologies"
        assert len(net.outputs) == 1, "Demo supports only single output topologies"

        input_blob = next(iter(net.inputs))
        out_blob = next(iter(net.outputs))

        lm_input_blob = next(iter(landmark_net.inputs))
        lm_out_blob = next(iter(landmark_net.outputs))

        log.info("Loading IR to the plugin...")
        exec_net = plugin.load(network=net, num_requests=2)
        lm_exec_net = plugin.load(network=landmark_net)

        # Read and pre-process input image
        n, c, h, w = net.inputs[input_blob].shape
        nm, cm, hm, wm = landmark_net.inputs[lm_input_blob].shape

        del net
        del landmark_net

        cap = cv2.VideoCapture("C:/Users/kang/Downloads/material/av77002671.mp4")

        cur_request_id = 0
        next_request_id = 1

        log.info("Starting inference in async mode...")
        log.info("To switch between sync and async modes press Tab button")
        log.info("To stop the demo execution press Esc button")
        is_async_mode = True
        render_time = 0
        ret, frame = cap.read()

        print(
            "To close the application, press 'CTRL+C' or any key with focus on the output window"
        )
        while cap.isOpened():
            if is_async_mode:
                ret, next_frame = cap.read()
            else:
                ret, frame = cap.read()
            if not ret:
                break
            initial_w = cap.get(3)
            initial_h = cap.get(4)
            inf_start = time.time()
            if is_async_mode:
                in_frame = cv2.resize(next_frame, (w, h))
                in_frame = in_frame.transpose(
                    (2, 0, 1))  # Change data layout from HWC to CHW
                in_frame = in_frame.reshape((n, c, h, w))
                exec_net.start_async(request_id=next_request_id,
                                    inputs={input_blob: in_frame})
            else:
                in_frame = cv2.resize(frame, (w, h))
                in_frame = in_frame.transpose(
                    (2, 0, 1))  # Change data layout from HWC to CHW
                in_frame = in_frame.reshape((n, c, h, w))
                exec_net.start_async(request_id=cur_request_id,
                                    inputs={input_blob: in_frame})
            if exec_net.requests[cur_request_id].wait(-1) == 0:

                res = exec_net.requests[cur_request_id].outputs[out_blob]
                for obj in res[0][0]:
                    if obj[2] > 0.5:
                        xmin = int(obj[3] * initial_w)
                        ymin = int(obj[4] * initial_h)
                        xmax = int(obj[5] * initial_w)
                        ymax = int(obj[6] * initial_h)
                        if xmin > 0 and ymin > 0 and (xmax < initial_w) and (
                                ymax < initial_h):
                            roi = frame[ymin:ymax, xmin:xmax, :]
                            rh, rw = roi.shape[:2]
                            face_roi = cv2.resize(roi, (wm, hm))
                            face_roi = face_roi.transpose((2, 0, 1))
                            face_roi = face_roi.reshape((nm, cm, hm, wm))
                            lm_exec_net.infer(inputs={'0': face_roi})
                            landmark_res = lm_exec_net.requests[0].outputs[
                                lm_out_blob]
                            landmark_res = np.reshape(landmark_res, (5, 2))
                            for m in range(len(landmark_res)):
                                x = landmark_res[m][0] * rw
                                y = landmark_res[m][1] * rh
                                cv2.circle(roi, (np.int32(x), np.int32(y)), 3,
                                        lut[m], 2, 8, 0)

                        cv2.rectangle(frame, (xmin, ymin), (xmax, ymax),
                                    (0, 0, 255), 2, 8, 0)

                inf_end = time.time()
                det_time = inf_end - inf_start

                # Draw performance stats
                inf_time_message = "Inference time: {:.3f} ms, FPS:{:.3f}".format(
                    det_time * 1000, 1000 / (det_time * 1000 + 1))
                render_time_message = "OpenCV rendering time: {:.3f} ms".format(
                    render_time * 1000)
                async_mode_message = "Async mode is on. Processing request {}".format(cur_request_id) if is_async_mode else \
                    "Async mode is off. Processing request {}".format(cur_request_id)

                cv2.putText(frame, inf_time_message, (15, 15),
                            cv2.FONT_HERSHEY_COMPLEX, 0.5, (200, 10, 10), 1)
                cv2.putText(frame, render_time_message, (15, 30),
                            cv2.FONT_HERSHEY_COMPLEX, 0.5, (10, 10, 200), 1)
                cv2.putText(frame, async_mode_message, (10, int(initial_h - 20)),
                            cv2.FONT_HERSHEY_COMPLEX, 0.5, (10, 10, 200), 1)

            #
            render_start = time.time()
            cv2.imshow("face detection", frame)
            render_end = time.time()
            render_time = render_end - render_start

            if is_async_mode:
                cur_request_id, next_request_id = next_request_id, cur_request_id
                frame = next_frame

            key = cv2.waitKey(1)
            if key == 27:
                break

        cv2.destroyAllWindows()
        del exec_net
        del lm_exec_net
        del plugin


    if __name__ == '__main__':
        sys.exit(face_landmark_demo() or 0)




转载请注明：[康瑶明的博客](https://luckykang.github.io)


