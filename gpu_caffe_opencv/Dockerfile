FROM nvidia/cuda:8.0-cudnn6-devel-ubuntu16.04
LABEL maintainer caffe-maint@googlegroups.com

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        vim \
        unzip \
        pkg-config \
        inetutils-ping \
        net-tools \
        python3 \
        python3-dev \
        libavcodec-dev \
        libavformat-dev \
        libswscale-dev \
        libgtk2.0-dev \
        libtbb2 \
        libtbb-dev \
        libjpeg-dev \
        libpng-dev \
        libtiff-dev \
        libjasper-dev \
        libdc1394-22-dev \
        libv4l-dev \
        liblapacke-dev \
        libxvidcore-dev \
        libx264-dev \
        libatlas-base-dev \
        gfortran \ 
        ffmpeg \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler && \
    rm -rf /var/lib/apt/lists/*
# install python dependencies
RUN wget https://bootstrap.pypa.io/get-pip.py && \
    python3 get-pip.py && \
    rm get-pip.py && \
    pip3 install numpy && \
    pip3 install scikit-image && \
    pip3 install protobuf && \
    pip3 install requests && \
    echo "alias python='/usr/bin/python3'" >> ~/.bashrc && \
    ln -s /usr/lib/x86_64-linux-gnu/libboost_python-py35.so /usr/lib/x86_64-linux-gnu/libboost_python3.so

WORKDIR /
COPY opencv-3.3.0.zip opencv-3.3.0.zip
RUN     unzip opencv-3.3.0.zip\
	&& mkdir /opencv-3.3.0/build \
	&& cd /opencv-3.3.0/build \
 	&& cmake -D CMAKE_BUILD_TYPE=RELEASE \
                  -D BUILD_PYTHON_SUPPORT=ON \
                  -D CMAKE_INSTALL_PREFIX=/usr/local \
                  -D WITH_FFMPEG=ON \
                  -D FORCE_VTK=ON \
                  -D WITH_TBB=ON \
                  -D WITH_V4L=ON \
                  -D WITH_QT=OFF \
                  -D WITH_OPENGL=ON \
                  -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-8.0 \
                  -D WITH_CUBLAS=ON \
                  -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" \
                  -D WITH_GDAL=ON \
                  -D WITH_XINE=ON \
                  -D PYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 \
                  -D BUILD_opencv_python3=ON \
                  -D BUILD_opencv_python2=OFF .. \
	&& make -j"$(nproc)" \
	&& make install \
	&& rm /opencv-3.3.0.zip \
	&& rm -r /opencv-3.3.0 


WORKDIR /root
#RUN git clone https://github.com/NVIDIA/nccl.git && \
COPY nccl nccl
COPY caffe-c3d1.1 caffe-c3d1.1
RUN  cd nccl && make -j install && \
     cd .. && rm -rf nccl && \
     cd caffe-c3d1.1 && \
     make all -j"$(nproc)" && \
     make pycaffe && \
     echo "export PYTHONPATH=/root/caffe-c3d1.1/python:$PYTHONPATH" >> ~/.bashrc && \
     ldconfig




WORKDIR /root
COPY openmpi-1.8.5.tar.bz2 openmpi-1.8.5.tar.bz2
RUN tar -jxvf openmpi-1.8.5.tar.bz2 && \
    cd openmpi-1.8.5 && \
    ./configure --prefix=/usr/local/openmpi && \
    make all -j"$(nproc)" && \
    make install && \
    echo "export PATH=/usr/local/openmpi/bin:$PATH" >> ~/.bashrc && \
    echo "export LD_LIBRARY_PATH=$PATH:/usr/local/openmpi/bin" >> ~/.bashrc && \
    echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/openmpi/lib" >> ~/.bashrc && \
    cd /root && \
    rm openmpi-1.8.5.tar.bz2 && \
    rm -r openmpi-1.8.5

WORKDIR /root
COPY matio-1.5.2.zip matio-1.5.2.zip
COPY caffe-pspnet caffe-pspnet
RUN unzip matio-1.5.2.zip && \
    cd matio-1.5.2 && \
    ./configure && \
    make && make check &&  make install &&\
    cd /root/caffe-pspnet && \
    make all -j"$(nproc)" && \
    make pycaffe && \
    cd /root && \
    rm matio-1.5.2.zip && \
    rm -r matio-1.5.2

WORKDIR /root
COPY caffe-segnet-cudnn7 caffe-segnet-cudnn7
RUN cd caffe-segnet-cudnn7 && \
    make all -j"$(nproc)" && \
    make pycaffe 




#ENV PYCAFFE_ROOT $CAFFE_ROOT/python
#ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
#ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
#RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

WORKDIR /home
