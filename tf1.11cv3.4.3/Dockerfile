FROM nvidia/cuda:9.0-cudnn7-devel
MAINTAINER yukuainan89 <yukuainan89@gmail.com>

RUN apt-get update && \
        apt-get install -y \
        wget \
        zlib1g-dev \
        libssl-dev \
        curl \
        build-essential \
        cmake \
        git \
        wget \
        unzip \
        yasm \
        pkg-config \
        libswscale-dev \
        libtbb2 \
        libtbb-dev \
        libjpeg-dev \
        libpng-dev \
        libtiff-dev \
        libavformat-dev \
        libpq-dev \
        libsm6 \
        libxext6 \
        libxrender1 \
        libfontconfig1 \
        vim

# install miniconda
RUN wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O Miniconda.sh && \
	/bin/bash Miniconda.sh -b -p /opt/conda && \
	rm Miniconda.sh

ENV PATH /opt/conda/bin:$PATH
RUN ln -s /opt/conda/bin/pip /opt/conda/bin/pip3

# setup python
RUN conda install python=3.5

# install tensorflow, pytorch, keras
RUN pip3 install --upgrade pip
RUN pip3 install --no-cache-dir tensorflow-gpu==1.11.0 torch==0.4.1 keras==2.2.4 torchvision
# install required python packages
COPY requirements.txt ./
RUN pip3 install --no-cache-dir -r requirements.txt

# install opencv
WORKDIR /
ENV OPENCV_VERSION="3.4.3"
RUN wget https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip \
    && unzip ${OPENCV_VERSION}.zip \
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
      -DCMAKE_INSTALL_PREFIX=$(python -c "import sys; print(sys.prefix)") \
      -DPYTHON_EXECUTABLE=$(which python) \
      -DPYTHON_INCLUDE_DIR=$(python -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
      -DPYTHON_PACKAGES_PATH=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
      .. \
    && make install \
    && rm /${OPENCV_VERSION}.zip \
    && rm -r /opencv-${OPENCV_VERSION}

# link cv to python library
#RUN ln -sf /usr/local/python/cv2 $(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())")
#ENV LD_LIBRARY_PATH /usr/local/lib:$LD_LIBRARY_PATH

ENV LANG C.UTF-8
