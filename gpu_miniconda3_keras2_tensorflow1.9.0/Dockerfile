FROM nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04


RUN apt-get update --fix-missing && apt-get install -y wget git
 
WORKDIR /root
COPY Miniconda3-latest-Linux-x86_64.sh anaconda.sh
RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
/bin/bash ~/anaconda.sh -b -p /opt/conda && \
rm ~/anaconda.sh


ENV PATH /opt/conda/bin:$PATH

WORKDIR /root
RUN conda install -c conda-forge ffmpeg && \
    conda install tensorflow-gpu=1.9.0 && \
    pip install keras && \
    conda install -c conda-forge opencv 
