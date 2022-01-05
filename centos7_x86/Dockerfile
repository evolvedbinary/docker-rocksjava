FROM i386/centos:7

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 7 (x86) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN linux32 yum --setopt=tsflags=nodocs -y update \
  && linux32 yum --setopt=tsflags=nodocs -y upgrade \
  && linux32 yum --setopt=tsflags=nodocs -y install gcc-c++ make perl which bzip2 \
  && linux32 yum --setopt=tsflags=nodocs -y install zlib-devel bzip2-devel lz4-devel snappy-devel \
  && linux32 yum --setopt=tsflags=nodocs -y install openssl openssl-devel libcurl-devel java-1.7.0-openjdk-devel wget \
  && linux32 yum -y clean all \
  && wget https://github.com/jemalloc/jemalloc/releases/download/3.6.0/jemalloc-3.6.0.tar.bz2 \
  && tar jxvf jemalloc-3.6.0.tar.bz2; cd jemalloc-3.6.0; linux32 ./configure; linux32 make; linux32 make install; cd ..; rm -rf jemalloc-3.6.0* \
  && wget https://cmake.org/files/v3.17/cmake-3.17.5.tar.gz \
  && tar zxvf cmake-3.17.5.tar.gz; cd cmake-3.17.5; linux32 ./bootstrap --system-curl; linux32 make; linux32 make install; cd ..; rm -rf cmake-3.17.5* \
  && wget https://github.com/facebook/zstd/releases/download/v1.5.0/zstd-1.5.0.tar.gz \
  && tar zxvf zstd-1.5.0.tar.gz; cd zstd-1.5.0; linux32 make; linux32 make install; cd ..; rm -rf zstd-1.5.0* \
  && wget -O gflags-2.0.tar.gz https://github.com/gflags/gflags/archive/refs/tags/v2.0.tar.gz \
  && tar zxvf gflags-2.0.tar.gz; cd gflags-2.0; linux32 ./configure ; linux32 make; linux32 make install; cd ..; rm -rf gflags-2.0*

ENV JAVA_HOME=/usr/lib/jvm/java-1.7.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
