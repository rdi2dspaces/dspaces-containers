# Build stage with Spack pre-installed and ready to be used
FROM spack/ubuntu-bionic:latest as builder

# What we want to install and how we want to install it
# is specified in a manifest file (spack.yaml)
RUN mkdir /opt/spack-environment \
&&  (echo "spack:" \
&&   echo "  specs:" \
&&   echo "  - mochi-margo ^mercury~boostsys ^argobots+debug+valgrind ^valgrind~boost" \
&&   echo "  - cmake" \
&&   echo "  - mpich" \
&&   echo "  view: /opt/view" \
&&   echo "  concretization: together" \
&&   echo "  config:" \
&&   echo "    install_tree: /opt/software") > /opt/spack-environment/spack.yaml

RUN apt-get update && apt-get install -y \
    pkg-config \
    autoconf \
    libxml2-dev \
    libtool \
    valgrind

# Install the software, remove unecessary deps
RUN cd /opt/spack-environment && spack env activate . && spack external find && spack config remove packages:openssl && spack install --fail-fast && spack gc -y

# Strip all the binaries
RUN find -L /opt/view/* -type f -exec readlink -f '{}' \; | \
    xargs file -i | \
    grep 'charset=binary' | \
    grep 'x-executable\|x-archive\|x-sharedlib' | \
    awk -F: '{print $1}' | xargs strip -s

# Modifications to the environment that are necessary to run
RUN cd /opt/spack-environment && \
    spack env activate --sh -d . >> /etc/profile.d/z10_spack_environment.sh

# Bare OS image to run the installed executables
FROM ubuntu:18.04

RUN apt-get update && apt-get install -y \
    pkg-config \
    autoconf \
    libxml2-dev \
    libtool

COPY --from=builder /opt/spack-environment /opt/spack-environment
COPY --from=builder /opt/software /opt/software
COPY --from=builder /opt/view /opt/view
COPY --from=builder /etc/profile.d/z10_spack_environment.sh /etc/profile.d/z10_spack_environment.sh
