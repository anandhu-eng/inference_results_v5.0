# syntax = docker/dockerfile:experimental
# based onhttps://github.com/pytorch/pytorch/blob/master/Dockerfile
#
# NOTE: To build this you will need a docker version > 18.06 with
#       experimental enabled and DOCKER_BUILDKIT=1
#
#       If you do not use buildkit you are not going to have a good time
#
#       For reference:
#           https://docs.docker.com/develop/develop-images/build_enhancements/

ARG BASE_IMAGE=intel/intel-optimized-pytorch:mlperf-inference-4.1-3dunet
FROM ${BASE_IMAGE} AS dev-base

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get autoclean && \
    apt-get autoremove -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends bc

RUN pip install py-libnuma==1.2

RUN rm -rf /workspace/*
COPY . /workspace

WORKDIR /workspace
RUN export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"} && \
    export CC=`which gcc` && export CXX=`which g++` && \
    rm -rf inference && \
    git clone --recurse-submodules https://github.com/mlcommons/inference.git && \
    cd inference && \
    git submodule update --init --recursive && cd loadgen && \
    CFLAGS="-std=c++14" python setup.py install 

RUN for FILE in $(cat /workspace/redactions.txt); do rm -rf /workspace/${FILE}; rm -rf /workspace/workload_code/${FILE}; done

ENV CONDA_PREFIX "/opt/conda"
ENV MODEL_DIR=/model
ENV DATA_DIR=/data
