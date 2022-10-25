FROM philipdavis/dspaces:bionic-margo

RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    gfortran \
    make \
    pkg-config \
    clang-format \
    software-properties-common \
    swig \
    python3-dev \
    python3-numpy

RUN add-apt-repository ppa:git-core/ppa -y

RUN apt-get update && apt-get install -y git
