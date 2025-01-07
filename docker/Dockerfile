FROM nvidia/cuda:12.4.1-devel-ubuntu22.04

LABEL name="instantmesh" maintainer="instantmesh"

RUN mkdir -p /workspace/instantmesh
WORKDIR /workspace

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y python3.10 python3-pip tzdata build-essential git wget vim libegl1-mesa-dev libglib2.0-0 unzip && \
    ln -fs /usr/share/zoneinfo/America/Chicago /etc/localtime && \ 
    dpkg-reconfigure --frontend noninteractive tzdata && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 xformers==0.0.29.post1 --index-url https://download.pytorch.org/whl/cu124
RUN pip install --no-cache-dir triton ninja

WORKDIR /workspace/instantmesh

ADD ./requirements.txt /workspace/instantmesh/requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY . /workspace/instantmesh

RUN chmod +x /workspace/instantmesh/docker/entrypoint.sh

ENV CONFIG="instant-mesh-large"

CMD ["sh", "-c", "/workspace/instantmesh/docker/entrypoint.sh"]