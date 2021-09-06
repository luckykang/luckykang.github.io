---
layout: post
title: Tools系列09-Dockerfile的实现&OpenVINO的demo编译
tag: Tools
---

### 一.源码编译benchmark_app

Dockerfile编写如下，先build一个ubuntu镜像，拉取github仓库上的openvino代码，然后进行源码编译，接着安装GPU相关的环境包，最后把benchmark_app依赖的库文件，和GPU相关环境拷贝到新建的ubuntu环境目录/usr/local中。

    FROM ubuntu:20.04 AS builder
    WORKDIR /home

    # workaround blocking by updating tzdata
    ENV TZ=Asia/Shanghai
    RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
    RUN apt-get update && \
        apt-get install -y -q --no-install-recommends wget \
            ca-certificates python3 cython3 python3-dev git \
            build-essential autoconf automake cmake libtool

    ARG DLDT_VER=2021.4
    ARG DLDT_REPO=https://github.com/openvinotoolkit/openvino.git

    RUN git clone --depth 1 -b ${DLDT_VER} ${DLDT_REPO} dldt && \
        cd dldt && \
        git submodule init && \
        git submodule update --recursive && \
        mkdir build && \
        cd build && \
        cmake -DCMAKE_BUILD_TYPE=Release \
            -DENABLE_MKL_DNN=ON -DENABLE_CLDNN=ON \
            -DENABLE_VPU=OFF -DENABLE_GNA=OFF \
            -DPYTHON_EXECUTABLE=/usr/bin/python3 .. && \
        make -j $(nproc)

    ARG igc_url=https://github.com/intel/intel-graphics-compiler/releases/download/igc-1.0.8365
    ARG ocl_url=https://github.com/intel/compute-runtime/releases/download/21.33.20678
    RUN mkdir /home/neo && cd /home/neo \
    && wget ${ocl_url}/intel-gmmlib_21.2.1_amd64.deb \
    && wget ${igc_url}/intel-igc-core_1.0.8365_amd64.deb \
    && wget ${igc_url}/intel-igc-opencl_1.0.8365_amd64.deb \
    && wget ${ocl_url}/intel-opencl_21.33.20678_amd64.deb \
    && wget ${ocl_url}/intel-ocloc_21.33.20678_amd64.deb \
    && wget ${ocl_url}/intel-level-zero-gpu_1.1.20678_amd64.deb

    FROM ubuntu:20.04

    ARG install_dir=/usr/local
    COPY --from=builder /home/neo/*.deb /home/neo/
    RUN cd /home/neo && dpkg -i intel*.deb && rm -rf /home/neo
    RUN apt-get update \
        && apt-get -y install --no-install-recommends ocl-icd-libopencl1 \
        && rm -rf /var/lib/apt/list/*

    #ENV LD_LIBRARY_PATH=${install_dir}/lib
    RUN mkdir -p ${install_dir}/lib && mkdir -p ${install_dir}/bin
    COPY --from=builder /home/dldt/bin/intel64/Release/benchmark_app ${install_dir}/bin/
    COPY --from=builder /home/dldt/bin/intel64/Release/lib/plugins.xml ${install_dir}/lib/
    COPY --from=builder /home/dldt/bin/intel64/Release/lib/*.so ${install_dir}/lib/
    COPY --from=builder /home/dldt/inference-engine/temp/tbb/lib/libtbb.so ${install_dir}/lib/
    COPY --from=builder /home/dldt/inference-engine/temp/tbb/lib/libtbb.so.2 ${install_dir}/lib/
    COPY --from=builder /home/dldt/inference-engine/temp/opencv_4.5.2_ubuntu20/opencv/lib/libopencv_core.so.4.5.2 ${install_dir}/lib/libopencv_core.so.4.5
    COPY --from=builder /home/dldt/inference-engine/temp/opencv_4.5.2_ubuntu20/opencv/lib/libopencv_imgcodecs.so.4.5.2 ${install_dir}/lib/libopencv_imgcodecs.so.4.5
    COPY --from=builder /home/dldt/inference-engine/temp/opencv_4.5.2_ubuntu20/opencv/lib/libopencv_imgproc.so.4.5.2 ${install_dir}/lib/libopencv_imgproc.so.4.5

运行容器

    docker run -it --volume /home/test/openvino_models/ir/intel/vehicle-license-plate-detection-barrier-0106/FP16:/home/models --device /dev/dri 5a254733464c

在container中使用GPU

    ./bin/benchmark_app -m /home/models/vehicle-license-plate-detection-barrier-0106.xml -niter 10 -d GPU

![20210906103225](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210906103225.png)


### 二.源码编译object_detection_sample_ssd

源码编译object_detection_sample_ssd，并且拷贝IR模型和媒体文件，

    FROM ubuntu:20.04 AS builder
    WORKDIR /home

    # workaround blocking by updating tzdata
    ENV TZ=Asia/Shanghai
    RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
    RUN apt-get update && \
        apt-get install -y -q --no-install-recommends wget \
            ca-certificates python3 python3-pip cython3 python3-dev git \
            build-essential autoconf automake cmake libtool


    ARG DLDT_VER=2021.4
    ARG DLDT_REPO=https://github.com/openvinotoolkit/openvino.git

    RUN git clone --depth 1 -b ${DLDT_VER} ${DLDT_REPO} dldt && \   
        cd  dldt && \
        git submodule init && \
        git submodule update --recursive && \
        mkdir build && \
        cd build && \
        cmake -DCMAKE_BUILD_TYPE=Release \
            -DENABLE_MKL_DNN=ON -DENABLE_CLDNN=ON \
            -DENABLE_VPU=OFF -DENABLE_GNA=OFF \
            -DPYTHON_EXECUTABLE=/usr/bin/python3 .. && \
        make -j $(nproc)



    RUN cd dldt && \
        git clone  https://github.com/openvinotoolkit/open_model_zoo.git  && \
        python3 -m pip install requests  pyyaml  && \
        cd open_model_zoo/tools/downloader && \
        python3 ./downloader.py  --name face-detection-retail-0005


    ARG igc_url=https://github.com/intel/intel-graphics-compiler/releases/download/igc-1.0.8365
    ARG ocl_url=https://github.com/intel/compute-runtime/releases/download/21.33.20678
    RUN mkdir /home/neo && cd /home/neo \
    && wget ${ocl_url}/intel-gmmlib_21.2.1_amd64.deb \
    && wget ${igc_url}/intel-igc-core_1.0.8365_amd64.deb \
    && wget ${igc_url}/intel-igc-opencl_1.0.8365_amd64.deb \
    && wget ${ocl_url}/intel-opencl_21.33.20678_amd64.deb \
    && wget ${ocl_url}/intel-ocloc_21.33.20678_amd64.deb \
    && wget ${ocl_url}/intel-level-zero-gpu_1.1.20678_amd64.deb




    FROM ubuntu:20.04

    ARG install_dir=/usr/local
    COPY --from=builder /home/neo/*.deb /home/neo/
    RUN cd /home/neo && dpkg -i intel*.deb && rm -rf /home/neo
    RUN apt-get update \
        && apt-get -y install --no-install-recommends ocl-icd-libopencl1 \
        && rm -rf /var/lib/apt/list/*

    #ENV LD_LIBRARY_PATH=${install_dir}/lib
    RUN mkdir -p ${install_dir}/lib && mkdir -p ${install_dir}/bin
    COPY --from=builder /home/dldt/bin/intel64/Release/object_detection_sample_ssd ${install_dir}/bin/
    COPY --from=builder /home/dldt/open_model_zoo/models/intel/face-detection-0205/assets/face-detection-0205.png ${install_dir}/bin/
    COPY --from=builder /home/dldt/open_model_zoo/tools/downloader/intel/face-detection-retail-0005/FP32/face-detection-retail-0005.xml ${install_dir}/bin/
    COPY --from=builder /home/dldt/open_model_zoo/tools/downloader/intel/face-detection-retail-0005/FP32/face-detection-retail-0005.bin ${install_dir}/bin/
    COPY --from=builder /home/dldt/bin/intel64/Release/lib/plugins.xml ${install_dir}/lib/
    COPY --from=builder /home/dldt/bin/intel64/Release/lib/*.so ${install_dir}/lib/
    COPY --from=builder /home/dldt/inference-engine/temp/tbb/lib/libtbb.so ${install_dir}/lib/
    COPY --from=builder /home/dldt/inference-engine/temp/tbb/lib/libtbb.so.2 ${install_dir}/lib/
    COPY --from=builder /home/dldt/inference-engine/temp/opencv_4.5.2_ubuntu20/opencv/lib/libopencv_core.so.4.5.2 ${install_dir}/lib/libopencv_core.so.4.5
    COPY --from=builder /home/dldt/inference-engine/temp/opencv_4.5.2_ubuntu20/opencv/lib/libopencv_imgcodecs.so.4.5.2 ${install_dir}/lib/libopencv_imgcodecs.so.4.5
    COPY --from=builder /home/dldt/inference-engine/temp/opencv_4.5.2_ubuntu20/opencv/lib/libopencv_imgproc.so.4.5.2 ${install_dir}/lib/libopencv_imgproc.so.4.5
    RUN echo ${install_dir}/lib > /etc/ld.so.conf.d/opencv.conf && ldconfig


运行结果

![20210906103308](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210906103308.png)

### 三.安装OpenVINO来编译multi_channel_face_detection_demo

先创建一个ubuntu镜像，然后下载OpenVINO并安装，在open_model_zoo的demo目录下执行build,编译完成后把模型、媒体文件以及相关依赖库COPY到新创建的镜像中，然后执行运行。



    FROM ubuntu:20.04 AS builder

    WORKDIR /home


    # workaround blocking by updating tzdata
    ENV TZ=Asia/Shanghai
    RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
    RUN apt-get update && \
        apt-get install -y -q --no-install-recommends wget \
            ca-certificates python3 python3-pip cython3 python3-dev libpython3-dev cpio libtbb-dev  libopencv-dev git \
                    build-essential autoconf automake cmake libtool && \
        python3 -m pip install requests  pyyaml 


    ARG PACKAGE_VER=l_openvino_toolkit_p_2021.4.582
    ARG OPENVINO_REPO=https://registrationcenter-download.intel.com/akdlm/irc_nas/17988/l_openvino_toolkit_p_2021.4.582.tgz
    ARG DEP_DIR=/opt/intel/openvino_2021/deployment_tools
    ARG OMZ_BUILD=/root/omz_demos_build

    RUN mkdir dldt && cd dldt  && \
        wget  ${OPENVINO_REPO}  && \
        tar -zxf  ${PACKAGE_VER}.tgz && \
        cd ${PACKAGE_VER} && \
        sed -i 's/decline/accept/g' silent.cfg && \
        ./install.sh -s silent.cfg --ignore-signature && \
        /opt/intel/openvino_2021/bin/setupvars.sh && \
        cd ${DEP_DIR}/open_model_zoo/demos && \
        ./build_demos.sh && \
        cd ${OMZ_BUILD} && \
        make -j4 && \
        cd ${DEP_DIR}/tools/model_downloader && \
        python3 ./downloader.py  --name face-detection-adas-0001


    ARG igc_url=https://github.com/intel/intel-graphics-compiler/releases/download/igc-1.0.8365
    ARG ocl_url=https://github.com/intel/compute-runtime/releases/download/21.33.20678
    RUN mkdir /home/neo && cd /home/neo \
    && wget ${ocl_url}/intel-gmmlib_21.2.1_amd64.deb \
    && wget ${igc_url}/intel-igc-core_1.0.8365_amd64.deb \
    && wget ${igc_url}/intel-igc-opencl_1.0.8365_amd64.deb \
    && wget ${ocl_url}/intel-opencl_21.33.20678_amd64.deb \
    && wget ${ocl_url}/intel-ocloc_21.33.20678_amd64.deb \
    && wget ${ocl_url}/intel-level-zero-gpu_1.1.20678_amd64.deb

    FROM ubuntu:20.04


    ARG install_dir=/usr/local
    COPY --from=builder /home/neo/*.deb /home/neo/
    RUN cd /home/neo && dpkg -i intel*.deb && rm -rf /home/neo
    RUN apt-get update \
        && apt-get -y install --no-install-recommends ocl-icd-libopencl1  \
        && rm -rf /var/lib/apt/list/*


    RUN mkdir -p ${install_dir}/lib && mkdir -p ${install_dir}/bin
    COPY --from=builder /root/omz_demos_build/intel64/Release/multi_channel_face_detection_demo ${install_dir}/bin/
    COPY --from=builder /opt/intel/openvino_2021/deployment_tools/open_model_zoo/models/intel/face-detection-adas-0001/assets/face-detection-adas-0001.png ${install_dir}/bin/
    COPY --from=builder /opt/intel/openvino_2021/deployment_tools/tools/model_downloader/intel/face-detection-adas-0001/FP32/face-detection-adas-0001.xml ${install_dir}/bin/
    COPY --from=builder /opt/intel/openvino_2021/deployment_tools/tools/model_downloader/intel/face-detection-adas-0001/FP32/face-detection-adas-0001.bin ${install_dir}/bin/
    COPY --from=builder /opt/intel/openvino_2021/opencv/lib ${install_dir}/lib/
    COPY --from=builder /opt/intel/openvino_2021/inference_engine/lib/intel64 ${install_dir}/lib/
    COPY --from=builder /opt/intel/openvino_2021/inference_engine/external/tbb/lib ${install_dir}/lib/
    COPY --from=builder /opt/intel/openvino_2021/deployment_tools/ngraph/lib ${install_dir}/lib/
    RUN echo ${install_dir}/lib > /etc/ld.so.conf.d/opencv.conf && ldconfig

运行结果

![20210906104857](https://cdn.jsdelivr.net/gh/luckykang/picture_bed/blogs_images/20210906104857.png)



















