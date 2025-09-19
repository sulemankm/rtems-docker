FROM rtems6-arm-tools:latest AS build
LABEL Description="rtems6 Raspberry Pi bsp"
ENV TERM linux
COPY config.ini /config.ini
RUN cd /usr/src && \
    git clone --depth 1 https://gitlab.rtems.org/rtems/rtos/rtems.git rtems && \
    cd rtems && \
    git fetch --all --tags && \
    git checkout tags/6.1 -b 6  && \
    mv /config.ini . && \
    ./waf configure \
    --prefix=/opt/rtems6 && \
    ./waf && \
    ./waf install
