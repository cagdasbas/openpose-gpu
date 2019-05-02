from nvidia/cuda:10.1-cudnn7-devel as build

# Get the dependencies
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
	ca-certificates curl git vim less wget \
	build-essential pkg-config python3-dev libopencv-dev libprotobuf-dev \
	libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler \
	libboost-all-dev libatlas-base-dev libgoogle-glog-dev liblmdb-dev libgflags-dev && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/*

# For some reason Ubuntu 18.04 cmake 10.2 cannot compile OpenPose, we need cmake 3.12.4
WORKDIR /
RUN wget https://github.com/Kitware/CMake/releases/download/v3.12.4/cmake-3.12.4-Linux-x86_64.tar.gz && \
	tar xf cmake-3.12.4-Linux-x86_64.tar.gz && \
	mv /cmake-3.12.4-Linux-x86_64 /cmake && \
	rm -f cmake-3.12.4-Linux-x86_64.tar.gz && \
	wget https://github.com/CMU-Perceptual-Computing-Lab/openpose/archive/v1.4.0.tar.gz && \
	tar xf v1.4.0.tar.gz && \
	mv /openpose-1.4.0 /openpose && \
	rm -f v1.4.0.tar.gz

RUN rm -rf /openpose/3rdparty/caffe && \
	git clone https://github.com/CMU-Perceptual-Computing-Lab/caffe.git /openpose/3rdparty/caffe && \
	mkdir /openpose/build

COPY Makefile.config Makefile /openpose/3rdparty/caffe/

WORKDIR /openpose/3rdparty/caffe/
RUN make -j8 && \
	make distribute DISTRIBUTE_DIR=/openpose/build/caffe/

WORKDIR /openpose/build

COPY Cuda.cmake /openpose/cmake/Cuda.cmake

RUN /cmake/bin/cmake -D CMAKE_BUILD_TYPE=RELEASE -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -D CMAKE_INSTALL_PREFIX=/opt/openpose -DBUILD_PYTHON=on -DDOWNLOAD_FACE_MODEL=OFF -D DOWNLOAD_HAND_MODEL=OFF -DBUILD_CAFFE=OFF -DCaffe_INCLUDE_DIRS=/openpose/build/caffe/include/ -DCaffe_LIBS=/openpose/build/caffe/lib/libcaffe.so .. && \
	make -j8 && \
	make install

RUN apt-get update && \
	apt-get install -y --no-install-recommends python3-pip python3-setuptools vim && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/* && \
	PATH=/cmake/bin/:$PATH pip3 install dlib

