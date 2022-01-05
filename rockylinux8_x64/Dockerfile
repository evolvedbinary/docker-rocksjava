FROM rockylinux:8

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="Rocky Linux 8 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN yum --setopt=tsflags=nodocs -y update \
  && yum --setopt=tsflags=nodocs -y install epel-release \
  && yum --setopt=tsflags=nodocs -y upgrade \
  && yum --setopt=tsflags=nodocs -y install cmake3 \
  && yum --setopt=tsflags=nodocs -y install gcc-c++ make perl which openssl wget \
  && yum --setopt=tsflags=nodocs -y install jemalloc-devel zlib-devel bzip2-devel lz4-devel snappy libzstd-devel \
  && yum --setopt=tsflags=nodocs -y install https://cdn.azul.com/zulu/bin/zulu-repo-1.0.0-1.noarch.rpm \
  && yum --setopt=tsflags=nodocs -y install zulu7-jdk \
  && yum -y clean all \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake 10 --slave /usr/local/bin/ctest ctest /usr/bin/ctest --slave /usr/local/bin/cpack cpack /usr/bin/cpack --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake3 20 --slave /usr/local/bin/ctest ctest /usr/bin/ctest3 --slave /usr/local/bin/cpack cpack /usr/bin/cpack3 --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake3 \
  && wget -O gflags-2.0.tar.gz https://github.com/gflags/gflags/archive/refs/tags/v2.0.tar.gz \
  && tar zxvf gflags-2.0.tar.gz; cd gflags-2.0; linux32 ./configure ; linux32 make; linux32 make install; cd ..; rm -rf gflags-2.0*

ENV JAVA_HOME=/usr/lib/jvm/java-1.7.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
