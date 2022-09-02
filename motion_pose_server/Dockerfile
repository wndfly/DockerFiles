FROM mediapipe_python_cudagl10.1_ubuntu18.04_py3.10:latest

MAINTAINER <wangming@motiontek.cn>

WORKDIR /

ENV OPENCV_VERSION=4.5.3
ENV OPENCVSHARP_VERSION=4.5.3.20210821

#COPY depends/* /

#RUN wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip && \
RUN wget https://www.m.motiontek.cn/opencv/opencv-${OPENCV_VERSION}.zip && \
    unzip opencv-${OPENCV_VERSION}.zip && rm opencv-${OPENCV_VERSION}.zip && \
    mv opencv-${OPENCV_VERSION} opencv && \
    #wget https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip && \
    wget https://www.m.motiontek.cn/opencv/opencv_contrib-${OPENCV_VERSION}.zip && \
    unzip opencv_contrib-${OPENCV_VERSION}.zip && rm opencv_contrib-${OPENCV_VERSION}.zip && \
    mv opencv_contrib-${OPENCV_VERSION} opencv_contrib && \
    cd opencv && mkdir build && cd build && \
    cmake -D OPENCV_EXTRA_MODULES_PATH=/opencv_contrib/modules \
    -D CMAKE_INSTALL_PREFIX=/opencv/install \
    -D CMAKE_BUILD_TYPE=RELEASE \
    -D BUILD_SHARED_LIBS=OFF \
    -D ENABLE_CXX11=ON \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_DOCS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_JAVA=OFF \
    -D BUILD_opencv_app=OFF \
    -D BUILD_opencv_barcode=OFF \
    -D BUILD_opencv_java_bindings_generator=OFF \
    -D BUILD_opencv_js_bindings_generator=OFF \
    -D BUILD_opencv_python_bindings_generator=OFF \
    -D BUILD_opencv_python_tests=OFF \
    -D BUILD_opencv_ts=OFF \
    -D BUILD_opencv_js=OFF \
    -D BUILD_opencv_bioinspired=OFF \
    -D BUILD_opencv_ccalib=OFF \
    -D BUILD_opencv_datasets=OFF \
    -D BUILD_opencv_dnn_objdetect=OFF \
    -D BUILD_opencv_dpm=OFF \
    -D BUILD_opencv_fuzzy=OFF \
    -D BUILD_opencv_gapi=OFF \
    -D BUILD_opencv_intensity_transform=OFF \
    -D BUILD_opencv_mcc=OFF \
    -D BUILD_opencv_objc_bindings_generator=OFF \
    -D BUILD_opencv_rapid=OFF \
    -D BUILD_opencv_reg=OFF \
    -D BUILD_opencv_stereo=OFF \
    -D BUILD_opencv_structured_light=OFF \
    -D BUILD_opencv_surface_matching=OFF \
    -D BUILD_opencv_videostab=OFF \
    -D BUILD_opencv_wechat_qrcode=OFF \
    -D WITH_GSTREAMER=OFF \
    -D WITH_ADE=OFF \
    -D OPENCV_ENABLE_NONFREE=ON .. && make -j$(nproc) && make install && ldconfig && cd / && \
    #cd / && wget https://github.com/shimat/opencvsharp/archive/${OPENCVSHARP_VERSION}.zip && \
    wget https://www.m.motiontek.cn/opencv/opencvsharp-${OPENCVSHARP_VERSION}.zip && \
    unzip opencvsharp-${OPENCVSHARP_VERSION}.zip && rm opencvsharp-${OPENCVSHARP_VERSION}.zip && \
    mv opencvsharp-${OPENCVSHARP_VERSION} opencvsharp && \
    mkdir /opencvsharp/make && cd /opencvsharp/make && \
    cmake -D CMAKE_INSTALL_PREFIX=/opencvsharp/make \
    -D OpenCV_DIR=/opencv/install/lib/cmake/opencv4 /opencvsharp/src && \
    make -j$(nproc) && make install && rm -rf /opencv && rm -rf /opencv_contrib && \
    cp /opencvsharp/make/OpenCvSharpExtern/libOpenCvSharpExtern.so /usr/lib/ && \
    rm -rf /opencvsharp

WORKDIR /app
COPY requirement_web.txt requirement.txt
RUN python -m pip install --upgrade pip && pip3 install -r requirement.txt

# ENV HTTP_PROXY=http://10.0.0.105:1087
# ENV HTTPS_PROXY=http://10.0.0.105:1087
# RUN python -c 'import mediapipe as mp; mp.solutions.pose._download_oss_pose_landmark_model(2); mp.solutions.pose._download_oss_pose_landmark_model(0)'
# ENV HTTP_PROXY=
# ENV HTTPS_PROXY=

COPY mp_old_model/* /usr/local/lib/python3.10/site-packages/mediapipe/modules/pose_landmark/
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
