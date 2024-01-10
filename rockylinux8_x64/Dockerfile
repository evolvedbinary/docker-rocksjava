FROM rockylinux:8

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="Rocky Linux 8 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN yum --setopt=tsflags=nodocs -y update \
  && yum --setopt=tsflags=nodocs -y install epel-release \
  && crb enable \
  && yum --setopt=tsflags=nodocs -y upgrade \
  && yum --setopt=tsflags=nodocs -y install cmake3 \
  && yum --setopt=tsflags=nodocs -y install gcc-c++ make perl which openssl curl wget git \
  && yum --setopt=tsflags=nodocs -y install gflags-devel jemalloc-devel zlib-devel bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && yum --setopt=tsflags=nodocs -y install java-1.8.0-openjdk-headless java-1.8.0-openjdk-devel \
  && yum -y clean all

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
