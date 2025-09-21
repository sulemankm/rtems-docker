FROM rtems6-qemu-xilinx:latest AS build
LABEL Description="rtems5 Microblaze tool chain"
ENV TERM linux

RUN cd /usr/src && \
    git clone --depth 1 https://gitlab.rtems.org/rtems/tools/rtems-source-builder.git rsb && \
    cd /usr/src/rsb && \
    git fetch --all --tags && \
    git checkout tags/6.1 -b 6  && \
    ./source-builder/sb-check && \
    cd /usr/src/rsb/rtems && \
    ../source-builder/sb-set-builder --prefix=/opt/rtems6 6/rtems-microblaze && \
    cd /usr/src && \
    rm -rf rsb

ENV PATH="/opt/rtems6/bin:${PATH}"

