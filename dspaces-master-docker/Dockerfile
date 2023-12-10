FROM philipdavis/dspaces:bionic-margo-plus

RUN mkdir /dspaces

RUN  mkdir /install \
  && mkdir /install/dspaces

RUN touch dataspaces.conf

RUN apt-get update

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y curl awscli jq
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y python3-pip libnetcdf-dev python3-netcdf4
RUN DEBIAN_FRONTEND=noninteractive apt update && apt upgrade -y
RUN DEBIAN_FRONTEND=noninteractive apt install -y wget build-essential libreadline-gplv2-dev libncursesw5-dev \
     libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev
RUN wget https://www.python.org/ftp/python/3.10.0/Python-3.10.0.tgz
RUN tar xzf Python-3.10.0.tgz
RUN cd Python-3.10.0 && ./configure --enable-optimizations && make install
RUN pip3 install -U setuptools
RUN pip3 install bitstring s3fs dill numpy

CMD . /etc/profile.d/z10_spack_environment.sh \
  && cd /dspaces \
  && git clone https://github.com/rdi2dspaces/dspaces \
  && mkdir /dspaces/dspaces/build \
  && cd - \
  && /dspaces/dspaces/scripts/build-and-run.sh ${REPO_BRANCH} /dspaces/dspaces /dspaces/dspaces/build /install/dspaces mpicc mpifort ${OFI_STRING}
