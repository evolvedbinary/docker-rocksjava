FROM centos:6

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 6 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

RUN sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/\$releasever/%baseurl=https://vault.centos.org/6.10/%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/\$releasever/%baseurl=https://vault.centos.org/6.10/%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && yum --setopt=tsflags=nodocs -y update \
  && yum --setopt=tsflags=nodocs -y install epel-release \
  && yum --setopt=tsflags=nodocs -y install centos-release-scl \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-SCLo-scl.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
  && sed -i s%#\\sbaseurl=http://mirror.centos.org/centos/6/%baseurl=https://vault.centos.org/6.10/%g /etc/yum.repos.d/CentOS-SCLo-scl.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/6/%baseurl=https://vault.centos.org/6.10/%g /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
  && yum --setopt=tsflags=nodocs -y upgrade \
  && yum --setopt=tsflags=nodocs -y install devtoolset-7 cmake3 \
  && yum --setopt=tsflags=nodocs -y install jemalloc-devel zlib-devel bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && yum --setopt=tsflags=nodocs -y install openssl java-1.8.0-openjdk-devel git wget \
  && yum -y clean all \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake 10 --slave /usr/local/bin/ctest ctest /usr/bin/ctest --slave /usr/local/bin/cpack cpack /usr/bin/cpack --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake3 20 --slave /usr/local/bin/ctest ctest /usr/bin/ctest3 --slave /usr/local/bin/cpack cpack /usr/bin/cpack3 --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake3 \
  && wget https://github.com/gflags/gflags/archive/v2.0.tar.gz -O gflags-2.0.tar.gz \
  && tar xvfz gflags-2.0.tar.gz; cd gflags-2.0; scl enable devtoolset-7 ./configure; scl enable devtoolset-7 make; scl enable devtoolset-7 'make install'; cd ..; rm -r gflags-2.0*

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
