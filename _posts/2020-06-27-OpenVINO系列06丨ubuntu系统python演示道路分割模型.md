---
layout: post
title: OpenVINO系列06丨ubuntu系统python演示道路分割模型
tag: OpenVINO
---

***

#### 效果展示  

![2020-06-27](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/2020-06-27.png)

***  

##  一、 操作步骤：

### 1. 安装OpenVINO

### 2. 执行下面命令，对samples编译
    cd /opt/intel/openvino/deployment_tools/inference_engine/samples
    ./build_samples.sh 

编译完成后，可以在home目录找到参数cpu_extension
    
    cpu_extension = "/home/kang/inference_engine_samples_build/intel64/Release/lib/libcpu_extension.so"

### 3. 下载模型，记录xml地址
road-segmentation-adas-0001

    model_xml = ""   
    model_bin = ""

##  二、 附录代码：
    import sys
    import cv2
    import numpy as np
    import time
    import logging as log
    from openvino.inference_engine import IENetwork, IEPlugin
    model_xml  = "/home/kang/open_model_zoo-2019/model_downloader/Transportation/segmentation/curbs/dldt/road-segmentation-adas-0001.xml"
    model_bin = "/home/kang/open_model_zoo-2019/model_downloader/Transportation/segmentation/curbs/dldt/road-segmentation-adas-0001.bin"
    plugin_dir = "/opt/intel/openvino/deployment_tools/inference_engine/lib/intel64"
    cpu_extension = "/home/kang/inference_engine_samples_build/intel64/Release/lib/libcpu_extension.so"


    def read_segmentation_demo():
        log.basicConfig(format="[ %(levelname)s ] %(message)s",
                        level=log.INFO,
                        stream=sys.stdout)
        # Plugin initialization for specified device and load extensions library if specified
        log.info("Initializing plugin for {} device...".format("CPU"))
        plugin = IEPlugin(device="CPU", plugin_dirs=plugin_dir)
        plugin.add_cpu_extension(cpu_extension)
        # Read IR
        log.info("Reading IR...")
        net = IENetwork(model=model_xml, weights=model_bin)

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
    log.info("Loading IR to the plugin...")
    exec_net = plugin.load(network=net, num_requests=2)
    # Read and pre-process input image
    n, c, h, w = net.inputs[input_blob].shape
    del net
    cap = cv2.VideoCapture("/home/kang/Downloads/openvino_sample_show-master/material/read_segmentation_demo.mp4")

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
        # 开启同步或者异步执行模式
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
            res = np.squeeze(res, 0)
            res = res.transpose(1, 2, 0)  
            res = np.argmax(res, 2)
            hh, ww = res.shape
            mask = np.zeros((hh, ww, 3), dtype=np.uint8)
            mask[np.where(res > 0)] = (0, 255, 255)
            mask[np.where(res > 1)] = (255, 0, 255)

            cv2.imshow("mask", mask)
            mask = cv2.resize(mask, dsize=(frame.shape[1], frame.shape[0]))
            frame = cv2.addWeighted(mask, 0.2, frame, 0.8, 0)

            inf_end = time.time()
            det_time = inf_end - inf_start

            # Draw performance stats
            inf_time_message = "Inference time: {:.3f} ms, FPS:{:.3f}".format(det_time * 1000, 1000 / (det_time*1000 + 1))
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


        render_start = time.time()
        cv2.imshow("segmentation Results", frame)
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
    del plugin


    if __name__ == '__main__':
    sys.exit(read_segmentation_demo() or 0)



<br>
转载请注明：[康瑶明的博客](https://luckykang.github.io) 


