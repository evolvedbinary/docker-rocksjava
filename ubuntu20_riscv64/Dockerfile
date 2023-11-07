FROM riscv64/ubuntu:20.04

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="Ubuntu 20.04 (riscv64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN apt-get -y update \
  && apt-get -y dist-upgrade \
  && apt-get -y install cmake \
  && apt-get -y install g++ make perl \
  && apt-get -y install libjemalloc-dev zlib1g-dev libbz2-dev liblz4-dev libsnappy-dev libzstd-dev libgflags-dev \
  && apt-get -y install openssl openjdk-11-jdk-headless wget curl

ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-riscv64
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
