FROM almalinux/8-base:8 AS almalinux8_x86-base-be

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="AlmaLinux 8 (x86) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# version of Apache Maven to install
ARG MAVEN_VERSION=3.9.12

# 1. Build a base AlmaLinux 8 Build Environment
RUN \
  linux32 dnf -y update \
  && linux32 dnf config-manager --set-enabled powertools \
  && linux32 dnf makecache \
  && linux32 dnf --setopt=tsflags=nodocs -y install hostname \
  && linux32 dnf --setopt=tsflags=nodocs -y install make perl python3 which bzip2 wget curl git \
  && linux32 dnf --setopt=tsflags=nodocs -y install gcc-toolset-12 \
  && linux32 dnf --setopt=tsflags=nodocs -y install cmake \
  && linux32 dnf -y clean all


# 2. Build a jemalloc-devel package as it is not available from EPEL for i686 (needed for RocksDB Build Environment)
FROM almalinux8_x86-base-be AS almalinux8_x86-jemalloc-be
RUN \
  linux32 dnf --setopt=tsflags=nodocs -y install rpm-build libxslt perl-generators valgrind-devel \
  && wget -P /tmp https://dl.fedoraproject.org/pub/epel/8/Everything/SRPMS/Packages/j/jemalloc-5.2.1-3.el8.src.rpm \
  && linux32 rpm -i /tmp/jemalloc-5.2.1-3.el8.src.rpm \
  && linux32 scl enable gcc-toolset-12 'rpmbuild -ba /root/rpmbuild/SPECS/jemalloc.spec'


# 3. Finally build the RocksDB Build Environment
FROM almalinux8_x86-base-be AS almalinux8_x86-rocksdb-be
COPY --from=almalinux8_x86-jemalloc-be /root/rpmbuild/RPMS/i686/jemalloc-5.2.1-3.el8.i686.rpm /tmp/jemalloc-5.2.1-3.el8.i686.rpm
COPY --from=almalinux8_x86-jemalloc-be /root/rpmbuild/RPMS/i686/jemalloc-devel-5.2.1-3.el8.i686.rpm /tmp/jemalloc-devel-5.2.1-3.el8.i686.rpm
COPY bellsoft.repo /etc/yum.repos.d/bellsoft.repo
RUN \
  linux32 dnf --setopt=tsflags=nodocs -y install /tmp/jemalloc-5.2.1-3.el8.i686.rpm /tmp/jemalloc-devel-5.2.1-3.el8.i686.rpm \
  && rm /tmp/jemalloc-5.2.1-3.el8.i686.rpm /tmp/jemalloc-devel-5.2.1-3.el8.i686.rpm \
  && linux32 dnf --setopt=tsflags=nodocs -y install gflags-devel snappy-devel zlib-devel bzip2-devel lz4-devel libzstd-devel \
  && linux32  dnf --setopt=tsflags=nodocs -y install bellsoft-java8-full \
  && linux32 dnf -y clean all \
  && wget -qO- https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -zxvf- -C /opt


ENV JAVA_HOME=/usr/lib/jvm/bellsoft-java8-full.i386
ENV M2_HOME=/opt/apache-maven-$MAVEN_VERSION
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
