FROM i386/centos:6

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 6 (x86) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

COPY CentOS-buildlogs-devtoolset-8.repo /etc/yum.repos.d/CentOS-buildlogs-devtoolset-8.repo

RUN curl --fail https://vault.centos.org/6.10/os/i386/Packages/udev-147-2.73.el6_8.2.i686.rpm --output /tmp/udev-147-2.73.el6_8.2.i686.rpm \
  && rpm -Uvh /tmp/udev-147-2.73.el6_8.2.i686.rpm --nodeps \
  && rm -f /tmp/udev-147-2.73.el6_8.2.i686.rpm \
  && curl --fail https://vault.centos.org/6.10/os/i386/Packages/util-linux-ng-2.17.2-12.28.el6_9.2.i686.rpm --output /tmp/util-linux-ng-2.17.2-12.28.el6_9.2.i686.rpm \
  && rpm -Uvh /tmp/util-linux-ng-2.17.2-12.28.el6_9.2.i686.rpm \
  && rm -f /tmp/util-linux-ng-2.17.2-12.28.el6_9.2.i686.rpm \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/\$releasever/%baseurl=https://vault.centos.org/6.10/%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/\$releasever/%baseurl=https://vault.centos.org/6.10/%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && linux32 yum --setopt=tsflags=nodocs -y reinstall udev.i686 util-linux-ng.i686 \
  && linux32 yum --setopt=tsflags=nodocs -y update \
  && linux32 yum --setopt=tsflags=nodocs -y install epel-release \
  && linux32 yum --setopt=tsflags=nodocs -y install wget tar which \
  && linux32 yum --setopt=tsflags=nodocs -y install make perl devtoolset-8 cmake3 \
  && linux32 yum --setopt=tsflags=nodocs -y install jemalloc-devel zlib-devel bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && linux32 yum --setopt=tsflags=nodocs -y install openssl java-1.8.0-openjdk-devel \
  && linux32 yum -y clean all \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake 10 --slave /usr/local/bin/ctest ctest /usr/bin/ctest --slave /usr/local/bin/cpack cpack /usr/bin/cpack --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake3 20 --slave /usr/local/bin/ctest ctest /usr/bin/ctest3 --slave /usr/local/bin/cpack cpack /usr/bin/cpack3 --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake3 \
  && curl --fail https://codeload.github.com/gflags/gflags/tar.gz/v2.0 --output /tmp/gflags-2.0.tar.gz \
  && cd /tmp && tar xvfz gflags-2.0.tar.gz; cd gflags-2.0; linux32 scl enable devtoolset-8 ./configure; linux32 scl enable devtoolset-8 make; linux32 scl enable devtoolset-8 'make install'; cd ..; rm -rf gflags-2.0*

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
