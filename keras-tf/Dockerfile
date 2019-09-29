ARG BASE_IMAGE=nvidia/cuda:10.0-cudnn7-runtime-ubuntu16.04
ARG TENSORFLOW=tensorflow-gpu
ARG TENSORFLOW_VER=1.13.2
FROM $BASE_IMAGE

ENV SHELL=/bin/bash
ENV HOME=/home/

RUN apt-get update && \
    apt-get install -y software-properties-common \
            vim nano curl wget git build-essential yasm make cmake gcc g++ \
            pkg-config libtbb2 libtbb-dev \
            libjpeg-dev libpng-dev libtiff-dev libpq-dev \
            libhdf5-dev libsm6 libxext6 libxrender-dev \
            libav-tools libx264-dev libx265-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Python3.6
RUN add-apt-repository ppa:jonathonf/python-3.6 && apt-get update && \
    apt-get install -y python3.6-dev python3-tk && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set Python3.6 to the default of Python3 and Python
RUN /usr/sbin/update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 0 && \
    /usr/sbin/update-alternatives --install /usr/bin/python python /usr/bin/python3.6 0

# Install pip for Python3.6
RUN curl https://bootstrap.pypa.io/get-pip.py | python3
# Install numpy since building opencv-python require that
RUN pip3 install numpy

# build opencv from source
ENV OPENCV_VERSION="3.4.2"
RUN curl -sSL -Os https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.tar.gz \
    && tar xzvf ${OPENCV_VERSION}.tar.gz \
    && mkdir /opencv-${OPENCV_VERSION}/cmake_binary \
    && cd /opencv-${OPENCV_VERSION}/cmake_binary \
    && cmake -DBUILD_TIFF=ON \
      -DBUILD_opencv_java=OFF \
      -DWITH_CUDA=OFF \
      -DWITH_OPENGL=ON \
      -DWITH_OPENCL=ON \
      -DWITH_IPP=ON \
      -DWITH_TBB=ON \
      -DWITH_EIGEN=ON \
      -DWITH_V4L=ON \
      -DBUILD_TESTS=OFF \
      -DBUILD_PERF_TESTS=OFF \
      -DCMAKE_BUILD_TYPE=RELEASE \
      -DCMAKE_INSTALL_PREFIX=$(python3 -c "import sys; print(sys.prefix)") \
      -DPYTHON_EXECUTABLE=/usr/bin/python3 \
      -DPYTHON3_LIBRARIES=/usr/lib/x86_64-linux-gnu/libpython3.6m.so \
      -DBUILD_opencv_python2=OFF \
      -DBUILD_opencv_python3=ON \
      .. \
     && make -j$nporc && make install \
     && rm /${OPENCV_VERSION}.tar.gz \
     && rm -r /opencv-${OPENCV_VERSION}

ADD pip-requirements.txt /requirements.txt
RUN pip3 install -r /requirements.txt
# RUN pip3 install ${TENSORFLOW}==${TENSORFLOW_VER}
RUN pip3 install tensorflow-gpu==1.13.2
