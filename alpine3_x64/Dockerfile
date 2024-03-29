FROM alpine:3.10

# update Alpine with latest versions
RUN echo '@edge http://nl.alpinelinux.org/alpine/edge/main' >> /etc/apk/repositories \
  && echo '@community http://nl.alpinelinux.org/alpine/edge/community' >> /etc/apk/repositories \
  && apk update \
  && apk upgrade

# install CA certificates
RUN apk add ca-certificates

# install build tools
RUN apk add \
  build-base \
  coreutils \
  file \
  git \
  perl \
  automake \
  autoconf \
  cmake

# install tool dependencies for building RocksDB static library
RUN apk add \
  curl \
  bash \
  wget \
  tar \
  openssl

# install RocksDB dependencies
RUN apk add \
  snappy snappy-dev \
  zlib zlib-dev \
  bzip2 bzip2-dev \
  lz4 lz4-dev \
  zstd zstd-dev \
  linux-headers

# install OpenJDK8
RUN apk add openjdk8 \
  && apk add java-cacerts

# cleanup
RUN rm -rf /var/cache/apk/*

# puts javac in the PATH
ENV JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk PATH=/usr/lib/jvm/java-1.8-openjdk/bin:$PATH

# gflags from source
RUN cd /tmp &&\
  git clone -b v2.0 --single-branch https://github.com/gflags/gflags.git &&\
  cd gflags &&\
  ./configure --prefix=/usr && make && make install &&\
  rm -rf /tmp/*
