FROM philipdavis/dspaces:bionic-margo-plus

RUN mkdir /dspaces

RUN  mkdir /install \
  && mkdir /install/dspaces

RUN touch dataspaces.conf

RUN apt-get update

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y curl awscli jq

CMD . /etc/profile.d/z10_spack_environment.sh \
  && cd /dspaces \
  && git clone https://github.com/rdi2dspaces/dspaces \
  && mkdir /dspaces/dspaces/build \
  && cd - \
  && /dspaces/dspaces/scripts/build-and-run.sh ${REPO_BRANCH} /dspaces/dspaces /dspaces/dspaces/build /install/dspaces mpicc mpifort ${OFI_STRING}
