FROM arm64v8/alpine:3.16

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="Alpine 3 (arm64v8) RocksJava Build Environment" \
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

# install BellSoft JDK 8
RUN echo 'https://apk.bell-sw.com/main' >> /etc/apk/repositories &&\
  wget -O /etc/apk/keys/info@bell-sw.com-5fea454e.rsa.pub https://apk.bell-sw.com/info@bell-sw.com-5fea454e.rsa.pub &&\
  apk add bellsoft-java8

# cleanup
RUN rm -rf /var/cache/apk/*

# puts javac in the PATH
ENV JAVA_HOME=/usr/lib/jvm/bellsoft-java8
ENV PATH=/usr/lib/jvm/bellsoft-java8/bin:$PATH

# gflags from source
RUN cd /tmp &&\
  git clone -b v2.0 --single-branch https://github.com/gflags/gflags.git &&\
  cd gflags &&\
  wget -O config.guess https://raw.githubusercontent.com/gcc-mirror/gcc/master/config.guess &&\
  wget -O config.sub https://raw.githubusercontent.com/gcc-mirror/gcc/master/config.sub &&\
  ./configure --prefix=/usr && make && make install &&\
  rm -rf /tmp/*
