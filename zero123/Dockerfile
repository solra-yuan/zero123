FROM loonfactory/cuda:11.7.1-cudnn8-devel-ubuntu20.04-python3.9 as cuda-sdk

RUN set -eux; apt-get update && apt-get install -y \
  curl \
  ca-certificates \
  sudo \
  git \
  bzip2 \
  libx11-6 \
  libglfw3-dev \
  libgles2-mesa-dev \
  libeigen3-dev \
  build-essential \ 
  cmake \
  pkg-config \
  unzip \
  yasm \
  checkinstall \
  libjpeg-dev \ 
  libpng-dev \
  libtiff-dev \
  libavcodec-dev \
  libavformat-dev \
  libswscale-dev \
  libavresample-dev \
  libgstreamer1.0-dev \
  libgstreamer-plugins-base1.0-dev \
  libxvidcore-dev \ 
  x264 \
  libx264-dev \ 
  libfaac-dev \ 
  libmp3lame-dev \
  libtheora-dev \
  libopencore-amrnb-dev \
  libopencore-amrwb-dev \
  libdc1394-22 \ 
  libdc1394-22-dev \
  libxine2-dev \ 
  libv4l-dev \
  v4l-utils \
  unzip

WORKDIR /usr/include/linux
RUN ln -s -f ../libv4l1-videodev.h videodev.h

WORKDIR /root
RUN set -eux; apt-get install -y libgtk-3-dev

RUN pip3 install -U pip numpy

RUN set -eux; \
      apt-get update; \ 
      apt-get install -y python3-testresources libtbb-dev libatlas-base-dev gfortran libprotobuf-dev protobuf-compiler libgoogle-glog-dev libgflags-dev libgphoto2-dev libeigen3-dev libhdf5-dev doxygen libhdf5-mpich-dev

# Set Working directory
WORKDIR /opt

ENV OPENCV_VERSION=4.5.5

# Install OpenCV from Source
RUN git clone --depth 1 --branch ${OPENCV_VERSION} https://github.com/opencv/opencv.git
RUN git clone --depth 1 --branch ${OPENCV_VERSION} https://github.com/opencv/opencv_contrib.git
RUN cd opencv && \
    mkdir build && \
    cd build && \
    cmake \
      -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/ \
      -D PYTHON3_PACKAGES_PATH=/usr/lib/python3/dist-packages \
      -D WITH_V4L=ON \
      -D WITH_QT=OFF \
      -D WITH_OPENGL=ON \
      -D WITH_GSTREAMER=ON \
      -D OPENCV_GENERATE_PKGCONFIG=ON \
      -D OPENCV_ENABLE_NONFREE=ON \
      -D OPENCV_EXTRA_MODULES_PATH=/opt/opencv_contrib/modules \
      -D INSTALL_PYTHON_EXAMPLES=OFF \
      -D INSTALL_C_EXAMPLES=OFF \
      -D ENABLE_FAST_MATH=1 \
      -D CUDA_FAST_MATH=1 \
      -D WITH_CUBLAS=1 \
      -D WITH_CUDA=ON \
      -D WITH_CUDNN=ON \
      -D OPENCV_DNN_CUDA=ON \
      -D CUDA_ARCH_BIN=7.5,8.6,8.7 \
      -D OPENCV_GENERATE_PKGCONFIG=ON \
      -D OPENCV_PC_FILE_NAME=opencv.pc \  
      -D BUILD_EXAMPLES=OFF .. && \
    make -j"$(nproc)" && \
    make install
 
ENV PYTHONPATH "$PYTHONPATH:/usr/lib/python3/dist-packages"

FROM cuda-sdk as zero-123

RUN mkdir /app
WORKDIR /app

RUN git clone https://github.com/solra-yuan/zero123.git
WORKDIR /app/zero123/zero123

RUN pip install -U setuptools
RUN pip install -r requirements.txt
RUN git clone https://github.com/CompVis/taming-transformers.git
RUN pip install -e taming-transformers/
RUN git clone https://github.com/openai/CLIP.git
RUN pip install -e CLIP/

CMD ["python3"]
