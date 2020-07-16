FROM ubuntu:bionic AS plugin_sfu
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get -y install --no-install-recommends build-essential \
    ca-certificates \
    curl \
    git \
    glib-2.0-dev \
    libglib2.0 \
    libjansson-dev && \
    apt-get clean && \
    ln -fs /usr/share/zoneinfo/America/Los_Angles /etc/localtime && \
    apt-get install -y tzdata && \
    dpkg-reconfigure --frontend noninteractive tzdata && \
    rm -rf /var/lib/apt/lists/*

RUN curl https://sh.rustup.rs | sh -s -- -y

RUN git clone https://github.com/mozilla/janus-plugin-sfu.git && \
    cd janus-plugin-sfu && \
    ~/.cargo/bin/cargo build --release

FROM ubuntu:bionic

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get -y install --no-install-recommends \
    automake \
    build-essential \
    ca-certificates \
    cmake \
    curl \
    gengetopt \
    git \
    libcurl4-openssl-dev \
    libglib2.0-dev \
    libjansson-dev \
    liblua5.3-dev \
    libmicrohttpd-dev \
    libnice-dev \
    libogg-dev \
    libopus-dev \
    libconfig-dev \
    libssl-dev \
    libsrtp-dev \
    libsofia-sip-ua-dev \
    libtool \
    pkg-config \
    sudo \
    wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN cd /tmp && \
    wget https://github.com/cisco/libsrtp/archive/v2.2.0.tar.gz && \
    tar fx v2.2.0.tar.gz && \
    cd libsrtp-2.2.0 && \
    ./configure --prefix=/usr --enable-openssl && \
    make shared_library && sudo make install
    
RUN cd /tmp && \
    git clone https://github.com/sctplab/usrsctp && \
    cd usrsctp/ && \
    ./bootstrap && \
    ./configure --prefix=/usr && \
    make && \
    make install && \
    cd /tmp && \
    git clone git://git.libwebsockets.org/libwebsockets && \
    cd libwebsockets/ && \
    mkdir build && \
    cd build && \
    cmake -DLWS_MAX_SMP=1 -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_C_FLAGS="-fpic" .. && \
    make && \
    make install

RUN cd /tmp && \
    git clone https://github.com/meetecho/janus-gateway.git && \
    cd janus-gateway && \
    sh autogen.sh && \
    ./configure --prefix=/opt/janus && \
    make && \
    make install && \
    make configs && \
    rm -rf /tmp/*

WORKDIR /opt/janus

COPY --from=plugin_sfu /janus-plugin-sfu/target/release/libjanus_plugin_sfu.so /opt/janus/lib/janus/plugins/

COPY ./janus/conf/janus.plugin.sfu.cfg /opt/janus/etc/janus/janus.plugin.sfu.cfg
COPY ./janus/conf/janus.transport.http.cfg /opt/janus/etc/janus/janus.transport.http.cfg
COPY ./janus/conf/janus.transport.websockets.cfg /opt/janus/etc/janus/janus.transport.websockets.cfg

EXPOSE 8989

CMD [ "./bin/janus" ]
