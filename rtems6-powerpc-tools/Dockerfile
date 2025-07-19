FROM sulemankm/rtems6-essential:latest AS build
LABEL Description="rtems6 PowerPC tool chain"
ENV TERM linux

ARG RTEMS_BASE=/opt/rtems6

RUN cd /usr/src && \
    git clone --depth 1 https://gitlab.rtems.org/rtems/tools/rtems-source-builder.git rsb && \
    cd /usr/src/rsb && \
    git fetch --all --tags && \
    git checkout base/6 -b 6 && \
    ./source-builder/sb-check && \
    cd /usr/src/rsb/rtems && \
    ../source-builder/sb-set-builder --list-bsets && \
    ../source-builder/sb-set-builder --prefix=${RTEMS_BASE} 6/rtems-powerpc && \
    cd /usr/src && \
    rm -rf rsb

# Check if gcc works
RUN ${RTEMS_BASE}/bin/powerpc-rtems6-gcc --version

ENV PATH="${RTEMS_BASE}/bin:${PATH}"
