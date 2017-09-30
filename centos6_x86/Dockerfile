FROM toopher/centos-i386:centos6

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 6 (x86) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN linux32 yum -y update \
  && linux32 yum --setopt=tsflags=nodocs -y install epel-release \
  && linux32 yum --setopt=tsflags=nodocs -y install wget tar \
  && wget -O /etc/yum.repos.d/devtools-2.repo https://people.centos.org/tru/devtools-2/devtools-2.repo \
  && linux32 yum --setopt=tsflags=nodocs -y install zlib-devel bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && linux32 yum --setopt=tsflags=nodocs -y install make perl devtoolset-2-binutils devtoolset-2-gcc devtoolset-2-gcc-c++ \
  && linux32 yum --setopt=tsflags=nodocs -y install openssl java-1.7.0-openjdk-devel \
  && linux32 yum -y clean all \
  && wget https://github.com/gflags/gflags/archive/v2.0.tar.gz -O gflags-2.0.tar.gz \
  && tar xvfz gflags-2.0.tar.gz; cd gflags-2.0; linux32 scl enable devtoolset-2 ./configure; linux32 scl enable devtoolset-2 make; linux32 scl enable devtoolset-2 'make install'; cd ..; rm -r gflags-2.0*

ENV JAVA_HOME=/usr/lib/jvm/java-1.7.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
