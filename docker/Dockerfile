# get the development image from nvidia cuda 12.1
FROM nvidia/cuda:12.4.1-base-ubuntu22.04

LABEL name="instantmesh" maintainer="instantmesh"

# create workspace folder and set it as working directory
RUN mkdir -p /workspace/instantmesh
WORKDIR /workspace

# Set the timezone
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y tzdata && \
    ln -fs /usr/share/zoneinfo/America/Chicago /etc/localtime && \ 
    dpkg-reconfigure --frontend noninteractive tzdata

# update package lists and install git, wget, vim, libegl1-mesa-dev, and libglib2.0-0
RUN apt-get update && \
    apt-get install -y build-essential git wget vim libegl1-mesa-dev libglib2.0-0 unzip

# install conda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    chmod +x Miniconda3-latest-Linux-x86_64.sh && \
    ./Miniconda3-latest-Linux-x86_64.sh -b -p /workspace/miniconda3 && \
    rm Miniconda3-latest-Linux-x86_64.sh

# update PATH environment variable
ENV PATH="/workspace/miniconda3/bin:${PATH}"

# initialize conda
RUN conda init bash

# create and activate conda environment
RUN conda create -n instantmesh python=3.10 && echo "source activate instantmesh" > ~/.bashrc
ENV PATH /workspace/miniconda3/envs/instantmesh/bin:$PATH

RUN conda install Ninja
RUN conda install cuda -c nvidia/label/cuda-12.4.1 -y

RUN pip install --no-cache-dir torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 xformers==0.0.29.post1 --index-url https://download.pytorch.org/whl/cu124
RUN pip install --no-cache-dir triton

RUN conda clean --tarballs -y

# change the working directory to the repository
WORKDIR /workspace/instantmesh

# other dependencies
ADD ./requirements.txt /workspace/instantmesh/requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY . /workspace/instantmesh

RUN chmod +x /workspace/instantmesh/docker/entrypoint.sh

ENV CONFIG="instant-mesh-large"

# Run the command when the container starts
CMD ["sh", "-c", "/workspace/instantmesh/docker/entrypoint.sh"]
