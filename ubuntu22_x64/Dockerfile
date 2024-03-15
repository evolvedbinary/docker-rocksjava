FROM ubuntu:22.04

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="Ubuntu 22.04 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN apt-get -y update \
  && apt-get -y dist-upgrade \
  && apt-get -y install cmake \
  && apt-get -y install g++ make perl \
  && apt-get -y install libjemalloc-dev zlib1g-dev libbz2-dev liblz4-dev libsnappy-dev libzstd-dev libgflags-dev \
  && apt-get -y install openssl openjdk-8-jdk-headless wget curl

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
