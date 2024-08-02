# Build stage with Spack pre-installed and ready to be used
FROM spack/ubuntu-jammy:latest as builder

# What we want to install and how we want to install it
# is specified in a manifest file (spack.yaml)
RUN mkdir -p /opt/spack-environment && \
set -o noclobber \
&&  (echo spack: \
&&   echo '  # add package specs to the `specs` list' \
&&   echo '  specs:' \
&&   echo '  - mochi-margo@0.14.1 ^mercury~boostsys@2.3.1 ^libfabric@1.19.0' \
&&   echo '  view: /opt/views/view' \
&&   echo '  compilers: ' \
&&   echo '  - compiler: ' \
&&   echo '      spec: gcc@=11.4.0' \
&&   echo '      paths:' \
&&   echo '        cc: /usr/bin/gcc' \
&&   echo '        cxx: /usr/bin/g++' \
&&   echo '        f77: /usr/bin/gfortran' \
&&   echo '        fc: /usr/bin/gfortran' \
&&   echo '      flags: ' \
&&   echo '        cflags: -g' \
&&   echo '      operating_system: ubuntu22.04' \
&&   echo '      target: x86_64' \
&&   echo '      modules: []' \
&&   echo '      environment: {}' \
&&   echo '      extra_rpaths: []' \
&&   echo '  concretizer:' \
&&   echo '    unify: true' \
&&   echo '  config:' \
&&   echo '    install_tree: /opt/software') > /opt/spack-environment/spack.yaml

# Install the software, remove unnecessary deps
RUN cd /opt/spack-environment && spack env activate . && spack install --fail-fast && spack gc -y

# Strip all the binaries
RUN find -L /opt/views/view/* -type f -exec readlink -f '{}' \; | \
    xargs file -i | \
    grep 'charset=binary' | \
    grep 'x-executable\|x-archive\|x-sharedlib' | \
    awk -F: '{print $1}' | xargs strip

# Modifications to the environment that are necessary to run
RUN cd /opt/spack-environment && \
    spack env activate --sh -d . > activate.sh

# Bare OS image to run the installed executables
FROM ubuntu:22.04

COPY --from=builder /opt/spack-environment /opt/spack-environment
COPY --from=builder /opt/software /opt/software

# paths.view is a symlink, so copy the parent to avoid dereferencing and duplicating it
COPY --from=builder /opt/views /opt/views

RUN { \
      echo '#!/bin/sh' \
      && echo '.' /opt/spack-environment/activate.sh \
      && echo 'exec "$@"'; \
    } > /entrypoint.sh \
&& chmod a+x /entrypoint.sh \
&& ln -s /opt/views/view /opt/view

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
    gcc \
    g++ \
    gfortran \
    make \
    pkg-config \
    clang-format \
    software-properties-common \
    swig \
    python3-dev \
    cmake \
    mpich \
    python3-pip \
    libnetcdf-dev \
    curl \
    liblz4-dev \
    jq
    
RUN pip3 install bitstring s3fs dill 'numpy<2.0.0' netcdf4 mpi4py netCDF4 xarray planetary_computer pystac_client fsspec

RUN add-apt-repository ppa:git-core/ppa -y

RUN apt-get update && apt-get install -y git
