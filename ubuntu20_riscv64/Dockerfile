FROM riscv64/ubuntu:20.04 AS ubuntu20_riscv64-base-be


LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="Ubuntu 20.04 (riscv64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# version of Apache Maven to install
ARG MAVEN_VERSION=3.9.12

# 1. Build a base Ubuntu 20 Build Environment
RUN apt-get -y update \
  && apt-get -y dist-upgrade \
  && apt-get -y install software-properties-common \
  && add-apt-repository -y ppa:ubuntu-toolchain-r/test \
  && apt-get -y update \
  && apt-get -y install make perl pthon3 which bzip2 wget curl git \
  && apt-get -y install gcc-11 g++-11 \
  && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 100 \
  && update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 100 \
  && apt-get -y install cmake \
  && apt-get -y install libjemalloc-dev \
  && apt-get -y autoremove \
  && apt-get -y clean


# 2. Finally build the RocksDB Build Environment
FROM ubuntu20_riscv64-base-be AS ubuntu20_riscv64-rocksdb-be
RUN \
  apt-get -y install libgflags-dev zlib1g-dev libbz2-dev liblz4-dev libsnappy-dev libzstd-dev \
  && apt-get -y install openssl openjdk-11-jdk-headless \
  && apt-get -y autoremove \
  && apt-get -y clean \
  && wget -qO- https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -zxvf- -C /opt


ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-riscv64
ENV M2_HOME=/opt/apache-maven-$MAVEN_VERSION
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
