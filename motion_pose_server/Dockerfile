FROM wndfly/mediapipe_python_cudagl10.1_ubuntu18.04_py3.9:latest

MAINTAINER <wangming@motiontek.cn>

WORKDIR /

ENV OPENCV_VERSION=4.6.0
ENV OPENCVSHARP_VERSION=4.6.0.20220608

#COPY depends/* /

RUN wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip && \
    unzip opencv-${OPENCV_VERSION}.zip && rm opencv-${OPENCV_VERSION}.zip && \
    mv opencv-${OPENCV_VERSION} opencv && \
    wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip && \
    unzip opencv_contrib-${OPENCV_VERSION}.zip && rm opencv_contrib-${OPENCV_VERSION}.zip && \
    mv opencv_contrib-${OPENCV_VERSION} opencv_contrib && \
    cd opencv && mkdir build && cd build && \
    cmake -D OPENCV_EXTRA_MODULES_PATH=/opencv_contrib/modules \
    -D CMAKE_INSTALL_PREFIX=/opencv/install -D CMAKE_BUILD_TYPE=RELEASE \
    -D BUILD_SHARED_LIBS=ON -D ENABLE_CXX11=ON -D BUILD_EXAMPLES=OFF \
    -D BUILD_DOCS=OFF -D BUILD_PERF_TESTS=OFF -D BUILD_TESTS=OFF \
    -D BUILD_JAVA=OFF -D BUILD_opencv_app=OFF -D BUILD_opencv_barcode=ON \
    -D BUILD_opencv_java_bindings_generator=OFF \
    -D BUILD_opencv_js_bindings_generator=OFF \
    -D BUILD_opencv_python_bindings_generator=OFF \
    -D BUILD_opencv_python_tests=OFF -D BUILD_opencv_ts=OFF \
    -D BUILD_opencv_js=OFF -D BUILD_opencv_bioinspired=OFF \
    -D BUILD_opencv_ccalib=OFF -D BUILD_opencv_datasets=OFF \
    -D BUILD_opencv_dnn_objdetect=OFF -D BUILD_opencv_dpm=OFF \
    -D BUILD_opencv_fuzzy=OFF -D BUILD_opencv_gapi=OFF \
    -D BUILD_opencv_intensity_transform=OFF -D BUILD_opencv_mcc=OFF \
    -D BUILD_opencv_objc_bindings_generator=OFF -D BUILD_opencv_rapid=OFF \
    -D BUILD_opencv_reg=OFF -D BUILD_opencv_stereo=OFF \
    -D BUILD_opencv_structured_light=OFF -D BUILD_opencv_surface_matching=OFF \
    -D BUILD_opencv_videostab=OFF -D BUILD_opencv_wechat_qrcode=ON \
    -D WITH_GSTREAMER=OFF -D WITH_ADE=OFF -D BUILD_JPEG=ON -D BUILD_OPENEXR=ON \
    -D BUILD_OPENJPEG=ON -D BUILD_PACKAGE=ON -D BUILD_PNG=ON -D BUILD_USE_SYMLINKS=ON \
    -D BUILD_WEBP=ON -D BUILD_ZLIB=ON -D ENABLE_FAST_MATH=ON -D MKL_USE_SINGLE_DYNAMIC_LIBRARY=ON \
    -D WITH_FREETYPE=ON -D WITH_JPEG=ON -D WITH_OPENEXR=ON -D WITH_OPENJPEG=ON -D WITH_PNG=ON \
    -D WITH_PROTOBUF=ON -D OPENCV_ENABLE_NONFREE=ON -D WITH_EIGEN=ON -D WITH_FFMPEG=ON .. && \
    make -j$(nproc) && make install && ldconfig && cd / && \
    wget https://github.com/shimat/opencvsharp/archive/${OPENCVSHARP_VERSION}.zip && \
    unzip opencvsharp-${OPENCVSHARP_VERSION}.zip && rm opencvsharp-${OPENCVSHARP_VERSION}.zip && \
    mv opencvsharp-${OPENCVSHARP_VERSION} opencvsharp && \
    mkdir /opencvsharp/make && cd /opencvsharp/make && \
    cmake -D CMAKE_INSTALL_PREFIX=/opencvsharp/make \
    -D OpenCV_DIR=/opencv/install/lib/cmake/opencv4 /opencvsharp/src && \
    make -j$(nproc) && make install && rm -rf /opencv && rm -rf /opencv_contrib && \
    cp /opencvsharp/make/OpenCvSharpExtern/libOpenCvSharpExtern.so /usr/lib/ && \
    rm -rf /opencvsharp

# openpose build begin
RUN apt remove -y cmake && apt update && \
    DEBIAN_FRONTEND=noninteractive apt install -y --no-install-recommends freeglut3 freeglut3-dev \
    libxmu-dev libxi-dev libeigen3-dev libgoogle-glog-dev libgflags-dev libatlas-base-dev libsuitesparse-dev \
    libopencv-dev libatlas-base-dev libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev \
    protobuf-compiler libboost-all-dev liblmdb-dev && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*

RUN mkdir /depends && cd /depends/ && \
    wget https://github.com/Kitware/CMake/releases/download/v3.24.2/cmake-3.24.2.tar.gz && \
    tar -xzf cmake-3.24.2.tar.gz && cd cmake-3.24.2/ && \
    ./configure && ./bootstrap && make -j`nproc` && make install -j`nproc` && cd / && rm -rf /depends

RUN rm /usr/bin/python3 && rm /usr/bin/python && \
    ln -s /usr/local/bin/python3 /usr/bin/python3 && \
    ln -s /usr/local/bin/python3 /usr/bin/python && \
    python -m pip install --upgrade numpy opencv-python
ENV PYTHON_EXECUTABLE=/usr/local/bin/python3.9
ENV PYTHON_LIBRARY=/usr/local/lib/libpython3.9.so
RUN mkdir /dl && cd /dl && \
    wget https://github.com/CMU-Perceptual-Computing-Lab/openpose/archive/refs/tags/v1.7.0.tar.gz && \
    wget https://github.com/pybind/pybind11/archive/085a29436a8c472caaaf7157aa644b571079bcaa.zip && \
    wget https://github.com/CMU-Perceptual-Computing-Lab/caffe/archive/b5ede488952e40861e84e51a9f9fd8fe2395cc8a.zip &&\
    unzip 085a29436a8c472caaaf7157aa644b571079bcaa.zip && unzip b5ede488952e40861e84e51a9f9fd8fe2395cc8a.zip && \
    tar -xzf v1.7.0.tar.gz && mv openpose-1.7.0 /openpose && \
    rm -rf /openpose/3rdparty/caffe && rm -rf /openpose/3rdparty/pybind11 && \
    mv caffe-b5ede488952e40861e84e51a9f9fd8fe2395cc8a /openpose/3rdparty/caffe && \
    mv pybind11-085a29436a8c472caaaf7157aa644b571079bcaa /openpose/3rdparty/pybind11 && \
    cd /openpose/ && mkdir build && cd build && \
    sed -ie 's/set(AMPERE "80 86")/#&/g'  ../cmake/Cuda.cmake && \
    sed -ie 's/set(AMPERE "80 86")/#&/g'  ../3rdparty/caffe/cmake/Cuda.cmake && \
    cmake .. -D DOWNLOAD_BODY_25_MODEL=ON -D DOWNLOAD_BODY_COCO_MODEL=ON -D DOWNLOAD_BODY_MPI_MODEL=ON \
    -D DOWNLOAD_FACE_MODEL=ON -D DOWNLOAD_HAND_MODEL=ON -D BUILD_PYTHON=ON -D PYBIND11_INSTALL=ON \
    -D PYTHON_EXECUTABLE=$PYTHON_EXECUTABLE -D PYTHON_LIBRARY=$PYTHON_LIBRARY -D BUILD_CAFFE=ON \
    -D BUILD_EXAMPLES=OFF -D BUILD_SHARED_LIBS=ON -D CMAKE_BUILD_TYPE=Release -D DL_FRAMEWORK=CAFFE \
    -D CUDA_USE_STATIC_CUDA_RUNTIME=ON -D GPU_MODE=CUDA -D CUDA_VERBOSE_BUILD=ON -D CUDA_ARCH=Manual \
    -D USE_CUDNN=OFF -D CUDA_ARCH_BIN="60 61 62 70 72 75" -D CUDA_ARCH_PTX=60 && \
    make -j `nproc` && make install && \
    mkdir /models/ && cd .. && cp -r models/face /models/ && \
    cp -r models/hand /models/ && cp -r models/pose /models/ && \
    rm -rf /dl && mv /openpose/build/ /build && rm -rf /openpose/ && mkdir /openpose/ && mv /build /openpose/
# openpose build end

WORKDIR /app
COPY requirement_web.txt requirement.txt
RUN python -m pip install --upgrade pip && pip3 install -r requirement.txt

# RUN python -c 'import mediapipe as mp; mp.solutions.pose._download_oss_pose_landmark_model(2); mp.solutions.pose._download_oss_pose_landmark_model(0)'
# ENV HTTP_PROXY=
# ENV HTTPS_PROXY=

COPY mp_old_model/* /usr/local/lib/python3.9/site-packages/mediapipe/modules/pose_landmark/
RUN unlink /etc/localtime && ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
#RUN apt-get update && apt-get install -y --no-install-recommends locales libgdiplus && \
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen

EXPOSE 8080/tcp
#COPY . .

ENV FLASK_DEBUG=1
ENV FLASK_APP=web
ENV FLASK_ENV=development
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8

CMD ["bash", "start.sh"]
#RUN mkdir -p logs && nohup bash start_celery.sh >> logs/celery.log 2>&1
#CMD ["python", "run.py"]
#CMD ["celery", "-A tasks.pose worker --loglevel=INFO --concurrency=1"]
