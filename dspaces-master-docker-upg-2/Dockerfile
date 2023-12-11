FROM philipdavis/dspaces:bionic-margo-plus

RUN mkdir /dspaces

RUN  mkdir /install \
  && mkdir /install/dspaces

RUN touch dataspaces.conf

RUN apt-get update && apt upgrade -y

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y ubuntu-release-upgrader-core
RUN DEBIAN_FRONTEND=noninteractive do-release-upgrade -f DistUpgradeViewNonInteractive
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y python3-pip libnetcdf-dev python3-netcdf4
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y curl awscli jq
RUN DEBIAN_FRONTEND=noninteractive apt install -y wget build-essential libreadline-gplv2-dev libncursesw5-dev \
     libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev
ARG version=3.27
ARG build=7
RUN wget https://cmake.org/files/v$version/cmake-$version.$build.tar.gz
RUN tar -xzvf cmake-$version.$build.tar.gz
RUN cd cmake-$version.$build/ && ./bootstrap && make && make install
RUN DEBIAN_FRONTEND=noninteractive apt remove -y python3-numpy
RUN DEBIAN_FRONTEND=noninteractive add-apt-repository -y ppa:deadsnakes/ppa
RUN DEBIAN_FRONTEND=noninteractive apt install -y python3.10 python3.10-dev
RUN wget https://bootstrap.pypa.io/get-pip.py
RUN ln -sf /usr/bin/python3.10 /usr/bin/python3
RUN python3.10 get-pip.py
RUN pip3.10 install bitstring s3fs dill numpy netcdf4 mpi4py
CMD . /etc/profile.d/z10_spack_environment.sh \
  && cd /dspaces \
  && git clone https://github.com/rdi2dspaces/dspaces \
  && mkdir /dspaces/dspaces/build \
  && cd - \
  && /dspaces/dspaces/scripts/build-and-run.sh ${REPO_BRANCH} /dspaces/dspaces /dspaces/dspaces/build /install/dspaces mpicc mpifort ${OFI_STRING}
