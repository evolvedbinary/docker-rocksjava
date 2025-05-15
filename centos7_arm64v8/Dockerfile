FROM arm64v8/centos:7

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="CentOS 7 (arm64v8) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/\$releasever/%baseurl=https://vault.centos.org/7.9.2009/%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/\$releasever/%baseurl=https://vault.centos.org/7.9.2009/%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && yum --setopt=tsflags=nodocs -y update \
  && yum --setopt=tsflags=nodocs -y install epel-release \
  && yum --setopt=tsflags=nodocs -y install centos-release-scl \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-SCLo-scl.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
  && sed -i s%#\\sbaseurl=http://mirror.centos.org/centos/7/%baseurl=https://vault.centos.org/7.9.2009/%g /etc/yum.repos.d/CentOS-SCLo-scl.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/7/%baseurl=https://vault.centos.org/7.9.2009/%g /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
  && yum --setopt=tsflags=nodocs -y upgrade \
  && yum --setopt=tsflags=nodocs -y install devtoolset-7 cmake3 \
  && yum --setopt=tsflags=nodocs -y install make perl which \
  && yum --setopt=tsflags=nodocs -y install jemalloc-devel zlib-devel bzip2-devel lz4-devel snappy-devel libzstd-devel gflags-devel \
  && yum --setopt=tsflags=nodocs -y install openssl java-1.8.0-openjdk-devel wget \
  && yum -y clean all \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake 10 --slave /usr/local/bin/ctest ctest /usr/bin/ctest --slave /usr/local/bin/cpack cpack /usr/bin/cpack --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake3 20 --slave /usr/local/bin/ctest ctest /usr/bin/ctest3 --slave /usr/local/bin/cpack cpack /usr/bin/cpack3 --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake3

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
