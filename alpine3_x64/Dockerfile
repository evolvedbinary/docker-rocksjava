FROM alpine:3.18

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="Alpine 3 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# make: Allow N jobs at once; infinite jobs with no arg
ARG JOBS=1

# version of gflags to compile
ARG GFLAGS_VERSION=2.2.2

# update Alpine with latest versions
RUN \
  apk update \
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

# install BellSoft JDK 8
RUN echo 'https://apk.bell-sw.com/main' >> /etc/apk/repositories &&\
  wget -O /etc/apk/keys/info@bell-sw.com-5fea454e.rsa.pub https://apk.bell-sw.com/info@bell-sw.com-5fea454e.rsa.pub &&\
  apk add bellsoft-java8

# cleanup
RUN rm -rf /var/cache/apk/*

# gflags from source
RUN \
  wget -O gflags-$GFLAGS_VERSION.tar.gz https://github.com/gflags/gflags/archive/refs/tags/v$GFLAGS_VERSION.tar.gz \
  && tar zxvf gflags-$GFLAGS_VERSION.tar.gz \
  && mkdir gflags-$GFLAGS_VERSION/build \
  && cd gflags-$GFLAGS_VERSION/build \
  && CMAKE_INSTALL_PREFIX=/usr cmake .. \
  && make -j $JOBS \
  && make install \
  && cd / \
  && rm -rf gflags-$GFLAGS_VERSION*

# puts javac in the PATH
ENV JAVA_HOME=/usr/lib/jvm/bellsoft-java8
ENV PATH=/usr/lib/jvm/bellsoft-java8/bin:$PATH
