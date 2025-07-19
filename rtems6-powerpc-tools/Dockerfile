FROM rtems6-essential:latest AS build
LABEL Description="rtems6 PowerPC tool chain"
ENV TERM linux

RUN cd /usr/src && \
    git clone --depth 1 https://gitlab.rtems.org/rtems/tools/rtems-source-builder.git rsb && \
    cd /usr/src/rsb && \
    ./source-builder/sb-check && \
    cd /usr/src/rsb/rtems && \
    ../source-builder/sb-set-builder --prefix=/opt/rtems6 6/rtems-powerpc && \
    cd /usr/src
#    rm -rf rsb \

# Check if gcc works
RUN /opt/rtems6/bin/powerpc-rtems6-gcc --version

ENV PATH="/opt/rtems6/bin:${PATH}"
