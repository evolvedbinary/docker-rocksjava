FROM centos:5.11

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS RocksJava" \
  vendor="Evolved Binary Ltd"

RUN yum -y update \
  && yum -y install wget curl \
  && wget -O /etc/yum.repos.d/devtools-2.repo https://people.centos.org/tru/devtools-2/devtools-2.repo \
  && yum -y --enablerepo=testing-devtools-2-centos-5 install make devtoolset-2-binutils devtoolset-2-gcc devtoolset-2-gcc-c++ \
  && yum -y install epel-release \
  && yum -y install zlib-devel bzip2-devel lz4-devel snappy-devel \
  && yum -y install openssl java-1.7.0-openjdk-devel \
  && yum -y clean all \
  && rm -rf /usr/share/docs/* /usr/share/man/*

# NOTE we manually remove /usr/share/docs and /usr/share/man, in CentOS 6+ we could use --setopt=tsflags=nodocs with YUM

ENV JAVA_HOME=/usr/lib/jvm/java-1.7.0
ENV PATH=$JAVA_HOME:$PATH
