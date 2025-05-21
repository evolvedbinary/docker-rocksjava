FROM i386/alpine:3.16

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="Alpine 3 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# update Alpine with latest versions
# RUN echo '@edge https://alpinelinux.mirror.garr.it/edge/main' > /etc/apk/repositories &&\
#   echo '@community https://alpinelinux.mirror.garr.it/edge/community' >> /etc/apk/repositories &&\
#   apk update &&\
#   apk upgrade
RUN apk update &&\
  apk upgrade

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
ENV JAVA_HOME=/usr/lib/jvm/java-1.8-openjdk
ENV PATH=/usr/lib/jvm/java-1.8-openjdk/bin:$PATH

# gflags from source
RUN cd /tmp &&\
  git clone -b v2.0 --single-branch https://github.com/gflags/gflags.git &&\
  cd gflags &&\
  ./configure --prefix=/usr && make && make install &&\
  rm -rf /tmp/*
