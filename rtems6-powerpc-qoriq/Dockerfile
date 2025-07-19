FROM rtems6-powerpc-tools:latest AS build
LABEL Description="rtems6 PowerPC bsp"
ENV TERM linux
COPY config.ini /config.ini
RUN cd /usr/src && \
    git clone --depth 1 https://gitlab.rtems.org/rtems/rtos/rtems.git rtems && \
    cd rtems && \
    mv /config.ini . && \
    ./waf configure \
    --prefix=/opt/rtems6 && \
    ./waf && \
    ./waf install
