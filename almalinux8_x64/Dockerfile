FROM almalinux/8-base:8 AS almalinux8_x64-base-be

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="AlmaLinux 8 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# version of Apache Maven to install
ARG MAVEN_VERSION=3.9.12

# 1. Build a base AlmaLinux 8 Build Environment
RUN \
  dnf -y update \
  && dnf config-manager --set-enabled powertools \
  && dnf --setopt=tsflags=nodocs -y install epel-release \
  && dnf makecache \
  && dnf --setopt=tsflags=nodocs -y install hostname \
  && dnf --setopt=tsflags=nodocs -y install make perl python3 which bzip2 wget curl git \
  && dnf --setopt=tsflags=nodocs -y install gcc-toolset-13 \
  && dnf --setopt=tsflags=nodocs -y install cmake \
  && dnf --setopt=tsflags=nodocs -y install jemalloc-devel \
  && dnf -y clean all


# 2. Finally build the RocksDB Build Environment
FROM almalinux8_x64-base-be AS almalinux8_x64-rocksdb-be
COPY bellsoft.repo /etc/yum.repos.d/bellsoft.repo
RUN \
  dnf --setopt=tsflags=nodocs -y install gflags-devel snappy-devel zlib-devel bzip2-devel lz4-devel libzstd-devel \
  && dnf --setopt=tsflags=nodocs -y install bellsoft-java8-full \
  && dnf -y clean all \
  && wget -qO- https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -zxvf- -C /opt


ENV JAVA_HOME=/usr/lib/jvm/bellsoft-java8-full.x86_64
ENV M2_HOME=/opt/apache-maven-$MAVEN_VERSION
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
