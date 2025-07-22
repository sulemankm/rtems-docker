ARG DEBIAN_VERSION=stable-20241016-slim
FROM debian:${DEBIAN_VERSION}
LABEL Description="RTEMS base build image"

ENV DEBIAN_FRONTEND=noninteractive
ENV TERM=linux

ARG PASSWORD="rtems"
ARG RTEMS_RTOS_VERSION=6
ARG VS_CODE_SERVER_VERSION=4.93.1
ARG VS_CODE_SERVER_PORT=8800
ARG VS_CODE_EXT_CPPTOOLS_VERSION=1.22.10
ARG VS_CODE_EXT_HEX_EDITOR_VERSION=1.11.1
ARG VS_CODE_EXT_CMAKETOOLS_VERSION=1.19.52
ARG VS_CODE_EXT_NRF_DEVICETREE_VERSION=2024.9.26
ARG VS_CODE_EXT_CLANGD_VERSION=0.2.0
ARG WGET_ARGS="-q --show-progress --progress=bar:force:noscroll"
ARG TARGETARCH="amd64"

# Set default shell during Docker image build to bash
SHELL ["/bin/bash", "-c"]

# Check if the target architecture is either x86_64 (amd64) or arm64 (aarch64)
RUN if [ "$TARGETARCH" = "amd64" ] || [ "$TARGETARCH" = "arm64" ]; then \
        echo "Architecture $TARGETARCH is supported."; \
    else \
        echo "Unsupported architecture: $TARGETARCH"; \
        exit 1; \
    fi

# Before Ubuntu 24.04, the sources.list file was used for additional sources
# RUN sed -i '/deb-src/s/^# //' /etc/apt/sources.list && apt update

# Since ubuntu 24.04, the sources.list.d directory is used for additional sources
# RUN cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources~
# RUN sed -Ei 's/^Types: deb$/Types: deb deb-src/' /etc/apt/sources.list.d/ubuntu.sources

# Standard build dependencies
RUN apt-get -y update && \
	apt-get install -y \
       apt-utils build-essential bear g++ gdb \
       dos2unix ca-certificates file locales \
       wget curl openssh-server mc \
       vim u-boot-tools git cmake \
       pax bison flex texinfo bzip2 \
       xz-utils unzip python3 python-is-python3 python3-pip python3-venv \
       libexpat1-dev libpython3-dev libncurses5-dev \
       python3-dev zlib1g-dev libtinfo-dev ninja-build pkg-config

# Clean up stale packages
RUN apt-get clean -y && \
	apt-get autoremove --purge -y && \
   rm -rf /var/lib/apt/lists/*

# Set up directories
RUN mkdir -p /workspace/

# Set up sshd working directory
RUN mkdir -p /var/run/sshd && \
    chmod 0755 /var/run/sshd

# Allow root login via SSH
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Expose SSH port
EXPOSE 22

# Use the "dark" theme for Midnight Commander
ENV MC_SKIN=dark

#-------------------------------------------------------------------------------
# VS Code Server

# Set VS Code Server environment variables
ENV VS_CODE_SERVER_VERSION=${VS_CODE_SERVER_VERSION}
ENV VS_CODE_SERVER_PORT=${VS_CODE_SERVER_PORT}

# Install VS Code Server
RUN cd /tmp && \
    wget ${WGET_ARGS} https://code-server.dev/install.sh && \
    chmod +x install.sh && \
    bash install.sh --version ${VS_CODE_SERVER_VERSION}

# Download VS Code extensions (code-server extension manager does not work well)
RUN cd /tmp && \
    if [ "$TARGETARCH" = "amd64" ]; then \
        wget ${WGET_ARGS} https://github.com/microsoft/vscode-cpptools/releases/download/v${VS_CODE_EXT_CPPTOOLS_VERSION}/cpptools-linux-x64.vsix -O cpptools.vsix; \
    elif [ "$TARGETARCH" = "arm64" ]; then \
        wget ${WGET_ARGS} https://github.com/microsoft/vscode-cpptools/releases/download/v${VS_CODE_EXT_CPPTOOLS_VERSION}/cpptools-linux-arm64.vsix -O cpptools.vsix; \
    else \
        echo "Unsupported architecture"; \
        exit 1; \
    fi && \
    wget ${WGET_ARGS} https://github.com/microsoft/vscode-cmake-tools/releases/download/v${VS_CODE_EXT_CMAKETOOLS_VERSION}/cmake-tools.vsix -O cmake-tools.vsix && \
    wget --compression=gzip ${WGET_ARGS} https://marketplace.visualstudio.com/_apis/public/gallery/publishers/ms-vscode/vsextensions/hexeditor/${VS_CODE_EXT_HEX_EDITOR_VERSION}/vspackage -O hexeditor.vsix && \
    wget --compression=gzip https://marketplace.visualstudio.com/_apis/public/gallery/publishers/nordic-semiconductor/vsextensions/nrf-devicetree/${VS_CODE_EXT_NRF_DEVICETREE_VERSION}/vspackage -O nordic-semiconductor.nrf-devicetree.vsix && \
    wget ${WGET_ARGS} https://github.com/clangd/vscode-clangd/releases/download/${VS_CODE_EXT_CLANGD_VERSION}/vscode-clangd-${VS_CODE_EXT_CLANGD_VERSION}.vsix -O clangd.vsix

# Install extensions
RUN cd /tmp && \
    code-server --install-extension cpptools.vsix && \
    code-server --install-extension cmake-tools.vsix && \
    code-server --install-extension hexeditor.vsix && \
    code-server --install-extension nordic-semiconductor.nrf-devicetree.vsix && \
    code-server --install-extension clangd.vsix

# Clean up
RUN cd /tmp && \
    rm install.sh && \
    rm cpptools.vsix && \
    rm cmake-tools.vsix && \
    rm hexeditor.vsix && \
    rm nordic-semiconductor.nrf-devicetree.vsix && \
    rm clangd.vsix

# Copy workspace configuration
COPY rtems.code-workspace /rtems.code-workspace

#-------------------------------------------------------------------------------
# Optional Settings

# Initialise system locale (required by menuconfig)
RUN sed -i '/^#.*en_US.UTF-8/s/^#//' /etc/locale.gen && \
    locale-gen en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8

RUN cd /workspace/
