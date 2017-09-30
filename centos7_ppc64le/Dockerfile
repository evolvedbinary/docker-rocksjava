FROM ppc64le/centos:7

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 7 (ppc64le) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN yum -y update \
  && yum --setopt=tsflags=nodocs -y install epel-release \
  && yum --setopt=tsflags=nodocs -y install wget which \
  && yum --setopt=tsflags=nodocs -y install gcc-c++ make perl \
  && yum --setopt=tsflags=nodocs -y install jemalloc-devel zlib-devel bzip2-devel lz4-devel snappy-devel libzstd-devel gflags-devel \
  && yum --setopt=tsflags=nodocs -y install openssl java-1.7.0-openjdk-devel \
  && yum -y clean all

ENV JAVA_HOME=/usr/lib/jvm/java-1.7.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
