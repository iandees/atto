FROM ubuntu:latest
CMD bash

# Install the toolchain to build mapbox-gl-native
RUN apt-get update \
 && apt-get -y install \
    apt-utils \
    build-essential \
    curl \
    git \
    tar \
    wget \
    zlib1g-dev \
    automake \
    libtool \
    xutils-dev \
    make \
    pkg-config \
    python-pip \
    libpng-dev \
    libsqlite3-dev \
    cmake \
    ninja-build \
    clang \
    nodejs \
    npm \
    libxi-dev \
    libglu1-mesa-dev \
    x11proto-randr-dev \
    x11proto-xext-dev \
    libxrandr-dev \
    x11proto-xf86vidmode-dev \
    libxxf86vm-dev \
    libxcursor-dev \
    libxinerama-dev \
    xvfb \
 && rm -rf /var/lib/apt/lists/*

# Clone and build mapbox-gl-native
RUN mkdir -p /opt/src/github.com/mapbox \
 && git clone \
    --depth=1 \
    --branch maps-v1.6.0 \
    https://github.com/mapbox/mapbox-gl-native /opt/src/github.com/mapbox/mapbox-gl-native \
 && cd /opt/src/github.com/mapbox/mapbox-gl-native \
 && git submodule update --init --recursive --depth=1
RUN cd /opt/src/github.com/mapbox/mapbox-gl-native/build \
 && cmake . -B build \
 && cmake --build build

# Clone and build mason-js
RUN npm cache clean -f
RUN npm install -g n
RUN n stable
RUN git -C /opt/src/github.com/mapbox clone https://github.com/mapbox/mason-js.git
WORKDIR /opt/src/github.com/mapbox/mason-js/
RUN npm install && npm link

# Get the latest Go binary
RUN curl -o /tmp/go.tar.gz https://storage.googleapis.com/golang/go1.14.5.linux-amd64.tar.gz
RUN tar -xf /tmp/go.tar.gz -C /usr/local/bin
ENV GOPATH=/opt/
ENV PATH=$PATH:/usr/local/bin/go/bin

ADD . /opt/src/github.com/go-spatial/atto
WORKDIR /opt/src/github.com/go-spatial/atto

RUN cp -r /opt/src/github.com/mapbox/mapbox-gl-native/mason_packages .
RUN mason-js install
RUN mason-js link

RUN cp /opt/src/github.com/mapbox/mapbox-gl-native/build/linux-x86_64/Release/*.a /opt/src/github.com/go-spatial/atto/mason_packages/.link/lib/
RUN cp -r /opt/src/github.com/mapbox/mapbox-gl-native/include/mbgl /opt/src/github.com/go-spatial/atto/mason_packages/.link/include/
RUN cp -r /opt/src/github.com/mapbox/mapbox-gl-native/platform/default/mbgl /opt/src/github.com/go-spatial/atto/mason_packages/.link/include/
RUN go build
