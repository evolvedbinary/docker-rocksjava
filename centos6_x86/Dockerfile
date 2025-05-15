FROM i386/centos:6 AS centos6_x86-base-be

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="CentOS 6 (x86) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# make: Allow N jobs at once; infinite jobs with no arg
ARG JOBS=1

# 1. Build a base CentOS 6 Build Environment
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
  && linux32 yum --setopt=tsflags=nodocs -y upgrade \
  && linux32 yum --setopt=tsflags=nodocs -y install make devtoolset-8 \
  && linux32 yum --setopt=tsflags=nodocs -y install openssl curl wget tar which perl \
  && linux32 yum --setopt=tsflags=nodocs -y install gettext-devel openssl-devel zlib-devel jemalloc-devel \
  && linux32 yum -y clean all


# 2. Build a newer nghttp (needed for libcurl)
FROM centos6_x86-base-be AS centos6_x86-nghttp2-be
RUN \
  wget --no-check-certificate https://github.com/nghttp2/nghttp2/releases/download/v1.58.0/nghttp2-1.58.0.tar.gz \
  && tar zxf nghttp2-1.58.0.tar.gz \
  && pushd nghttp2-1.58.0 \
  && linux32 scl enable devtoolset-8 './configure --prefix=/usr --with-openssl --with-zlib --with-jemalloc --enable-lib-only' \
  && linux32 scl enable devtoolset-8 'make -j $JOBS' \
  && linux32 scl enable devtoolset-8 'make install'


# 3. Build a newer libcurl (needed for git)
FROM centos6_x86-base-be AS centos6_x86-curl-be
COPY --from=centos6_x86-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos6_x86-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a 
COPY --from=centos6_x86-nghttp2-be /usr/lib/libnghttp2.so.14.25.1 /usr/lib/libnghttp2.so.14.25.1
COPY --from=centos6_x86-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos6_x86-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
COPY --from=centos6_x86-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so.14 \
  && linux32 ldconfig \
  && popd \
  && linux32 yum --setopt=tsflags=nodocs -y install libev-devel libssh2-devel \
  && wget --no-check-certificate https://curl.haxx.se/download/curl-8.5.0.tar.gz \
  && tar zxf curl-8.5.0.tar.gz \
  && pushd curl-8.5.0 \
  && linux32 scl enable devtoolset-8 './configure --prefix=/usr --enable-ipv6 --enable-unix-sockets --with-ssl --with-libssh2' \
  && linux32 scl enable devtoolset-8 'make -j $JOBS' \
  && linux32 scl enable devtoolset-8 'make install'


# 4. Build a newer git (needed for CircleCI to use `git -B` in its `checkout` step)
FROM centos6_x86-base-be AS centos6_x86-git-be
COPY --from=centos6_x86-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos6_x86-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a 
COPY --from=centos6_x86-nghttp2-be /usr/lib/libnghttp2.so.14.25.1 /usr/lib/libnghttp2.so.14.25.1
COPY --from=centos6_x86-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos6_x86-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
COPY --from=centos6_x86-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
COPY --from=centos6_x86-curl-be /usr/bin/curl /usr/bin/curl
COPY --from=centos6_x86-curl-be /usr/bin/curl-config /usr/bin/curl-config
COPY --from=centos6_x86-curl-be /usr/include/curl /usr/include/curl
COPY --from=centos6_x86-curl-be /usr/lib/libcurl.a /usr/lib/libcurl.a
COPY --from=centos6_x86-curl-be /usr/lib/libcurl.la /usr/lib/libcurl.la
COPY --from=centos6_x86-curl-be /usr/lib/libcurl.so.4.8.0 /usr/lib/libcurl.so.4.8.0
COPY --from=centos6_x86-curl-be /usr/lib/pkgconfig/libcurl.pc /usr/lib/pkgconfig/libcurl.pc
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so.14 \
  && ln -s libcurl.so.4.8.0 libcurl.so \
  && mv libcurl.so.4.1.1 libcurl.so.4.1.1.OLD \
  && unlink libcurl.so.4 \
  && ln -s libcurl.so.4.8.0 libcurl.so.4 \
  && linux32 ldconfig \
  && popd \
  && linux32 yum --setopt=tsflags=nodocs -y install autoconf \
  && wget --no-check-certificate https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.43.0.tar.gz \
  && tar zxf git-2.43.0.tar.gz \
  && pushd git-2.43.0 \
  && linux32 scl enable devtoolset-8 'make configure' \
  && linux32 scl enable devtoolset-8 'NO_PERL=YesPlease NO_TCLTK=YesPlease ./configure --prefix=/usr' \
  && linux32 scl enable devtoolset-8 'make -j $JOBS all' \
  && linux32 scl enable devtoolset-8 'make install'


# 5. Build a newer version of gflags (needed for RocksDB)
FROM centos6_x86-base-be AS centos6_x86-gflags-be
RUN \
  wget --no-check-certificate https://github.com/gflags/gflags/archive/v2.0.tar.gz -O gflags-2.0.tar.gz \
  && tar zxf gflags-2.0.tar.gz \
  && pushd gflags-2.0 \
  && linux32 scl enable devtoolset-8 './configure --prefix=/usr' \
  && linux32 scl enable devtoolset-8 'make -j $JOBS' \
  && linux32 scl enable devtoolset-8 'make install'


# 6. Finally build the RocksDB Build Environment
FROM centos6_x86-base-be AS centos6_x86-rocksdb-be
COPY --from=centos6_x86-git-be /usr/bin/git /usr/bin/git
COPY --from=centos6_x86-git-be /usr/libexec/git-core /usr/libexec/git-core
COPY --from=centos6_x86-git-be /usr/share/git-core /usr/share/git-core
COPY --from=centos6_x86-gflags-be /usr/lib/libgflags.a /usr/lib/libgflags.la /usr/lib/libgflags.so.2.1.0 /usr/lib/libgflags_nothreads.a /usr/lib/libgflags_nothreads.la /usr/lib/libgflags_nothreads.so.2.1.0 /usr/lib/
COPY --from=centos6_x86-gflags-be /usr/include/gflags /usr/include/gflags
COPY --from=centos6_x86-gflags-be /usr/include/google /usr/include/google
COPY --from=centos6_x86-gflags-be /usr/lib/pkgconfig/libgflags* /usr/lib/pkgconfig/
RUN \
  pushd /usr/lib \
  && ln -s libgflags.so.2.1.0 libgflags.so \
  && ln -s libgflags.so.2.1.0 libgflags.so.2 \
  && ln -s libgflags_nothreads.so.2.1.0 libgflags_nothreads.so \
  && ln -s libgflags_nothreads.so.2.1.0 libgflags_nothreads.so.2 \
  && ldconfig \
  && popd \
  && linux32 yum --setopt=tsflags=nodocs -y install cmake3 \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake 10 --slave /usr/local/bin/ctest ctest /usr/bin/ctest --slave /usr/local/bin/cpack cpack /usr/bin/cpack --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake3 20 --slave /usr/local/bin/ctest ctest /usr/bin/ctest3 --slave /usr/local/bin/cpack cpack /usr/bin/cpack3 --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake3 \
  && linux32 yum --setopt=tsflags=nodocs -y install bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && linux32 yum --setopt=tsflags=nodocs -y install java-1.8.0-openjdk-devel \
  && yum -y clean all

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
