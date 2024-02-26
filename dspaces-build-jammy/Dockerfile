FROM philipdavis/dspaces-deps:jammy

ARG REPO_BRANCH=netcdf
RUN mkdir -p /build/dataspaces
RUN mkdir -p /opt/dspaces
RUN . /opt/spack-environment/activate.sh \   
    && git clone https://github.com/rdi2dspaces/dspaces --single-branch --branch ${REPO_BRANCH} \
    && cmake -S /dspaces -B /build/dspaces -DCMAKE_INSTALL_PREFIX=/opt/dspaces -DENABLE_TESTS=True -DCMAKE_C_COMPILER=mpicc -DCMAKE_Fortran_COMPILER=mpifort \
    && cmake --build /build/dspaces --target install --config RelWithDebInfo

RUN { \
      echo '#!/bin/sh' \
      && echo '.' /opt/spack-environment/activate.sh \
      && echo "export CMAKE_PREFIX_PATH=/opt/dspaces/:${CMAKE_PREFIX_PATH}" \
      && echo "export LD_LIBRARY_PATH=/opt/dspaces/lib:${LD_LIBRARY_PATH}" \
      && echo "export PATH=/opt/dspaces/bin:${PATH}" \
      && echo "export PKG_CONFIG_PATH=/opt/dspaces/lib/pkgconfig:${PKG_CONFIG_PATH}" \
      && echo "export PYTHONPATH=/opt/dspaces/lib/python3/dist-packages:${PYTHONPATH}" \
      && echo 'exec "$@"'; \
    } > /entrypoint.sh \
&& chmod a+x /entrypoint.sh 

ENTRYPOINT [ "/entrypoint.sh" ]
