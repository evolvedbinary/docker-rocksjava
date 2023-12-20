FROM centos:6 AS centos6_x64-base-be

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 6 (x64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# make: Allow N jobs at once; infinite jobs with no arg
ARG JOBS=1


# 1. Build a base CentOS 6 Build Environment
RUN \
  sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-Base.repo \
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
  && yum --setopt=tsflags=nodocs -y install devtoolset-7 \
  && yum --setopt=tsflags=nodocs -y install openssl curl wget \
  && yum --setopt=tsflags=nodocs -y install gettext-devel openssl-devel zlib-devel jemalloc-devel \
  && yum -y clean all


# 2. Build a newer nghttp (needed for libcurl)
FROM centos6_x64-base-be AS centos6_x64-nghttp2-be
RUN \
  wget --no-check-certificate https://github.com/nghttp2/nghttp2/releases/download/v1.58.0/nghttp2-1.58.0.tar.gz \
  && tar zxf nghttp2-1.58.0.tar.gz \
  && pushd nghttp2-1.58.0 \
  && scl enable devtoolset-7 './configure --prefix=/usr --with-openssl --with-zlib --with-jemalloc --enable-lib-only' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install'


# 3. Build a newer libcurl (needed for git)
FROM centos6_x64-base-be AS centos6_x64-curl-be
COPY --from=centos6_x64-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos6_x64-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a 
COPY --from=centos6_x64-nghttp2-be /usr/lib/libnghttp2.so.14.25.1 /usr/lib/libnghttp2.so.14.25.1
COPY --from=centos6_x64-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos6_x64-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
COPY --from=centos6_x64-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so.14 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install libev-devel libssh2-devel \
  && wget --no-check-certificate https://curl.haxx.se/download/curl-8.5.0.tar.gz \
  && tar zxf curl-8.5.0.tar.gz \
  && pushd curl-8.5.0 \
  && scl enable devtoolset-7 './configure --prefix=/usr --enable-ipv6 --enable-unix-sockets --with-ssl --with-libssh2' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install'


# 4. Build a newer git (needed for CircleCI to use `git -B` in its `checkout` step)
FROM centos6_x64-base-be AS centos6_x64-git-be
COPY --from=centos6_x64-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos6_x64-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a 
COPY --from=centos6_x64-nghttp2-be /usr/lib/libnghttp2.so.14.25.1 /usr/lib/libnghttp2.so.14.25.1
COPY --from=centos6_x64-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos6_x64-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
COPY --from=centos6_x64-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
COPY --from=centos6_x64-curl-be /usr/bin/curl /usr/bin/curl
COPY --from=centos6_x64-curl-be /usr/bin/curl-config /usr/bin/curl-config
COPY --from=centos6_x64-curl-be /usr/include/curl /usr/include/curl
COPY --from=centos6_x64-curl-be /usr/lib/libcurl.a /usr/lib/libcurl.a
COPY --from=centos6_x64-curl-be /usr/lib/libcurl.la /usr/lib/libcurl.la
COPY --from=centos6_x64-curl-be /usr/lib/libcurl.so.4.8.0 /usr/lib/libcurl.so.4.8.0
COPY --from=centos6_x64-curl-be /usr/lib/pkgconfig/libcurl.pc /usr/lib/pkgconfig/libcurl.pc
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so \
  && ln -s libnghttp2.so.14.25.1 libnghttp2.so.14 \
  && ln -s libcurl.so.4.8.0 libcurl.so \
  && ln -s libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && pushd /usr/lib64 \
  && mv libcurl.so.4.1.1 libcurl.so.4.1.1.OLD \
  && unlink libcurl.so.4 \
  && ln -s /usr/lib/libcurl.so.4 libcurl.so.4 \
  && popd \
  && yum --setopt=tsflags=nodocs -y install autoconf \
  && wget --no-check-certificate https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.43.0.tar.gz \
  && tar zxf git-2.43.0.tar.gz \
  && pushd git-2.43.0 \
  && scl enable devtoolset-7 'make configure' \
  && scl enable devtoolset-7 'NO_PERL=YesPlease NO_TCLTK=YesPlease ./configure --prefix=/usr' \
  && scl enable devtoolset-7 'make -j $JOBS all' \
  && scl enable devtoolset-7 'make install'


# 5. Build a newer version of gflags (needed for RocksDB)
FROM centos6_x64-base-be AS centos6_x64-gflags-be
RUN \
  wget --no-check-certificate https://github.com/gflags/gflags/archive/v2.0.tar.gz -O gflags-2.0.tar.gz \
  && tar zxf gflags-2.0.tar.gz \
  && pushd gflags-2.0 \
  && scl enable devtoolset-7 './configure --prefix=/usr' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install'


# 6. Finally build the RocksDB Build Environment
FROM centos6_x64-base-be AS centos6_x64-rocksdb-be
COPY --from=centos6_x64-git-be /usr/bin/git /usr/bin/git
COPY --from=centos6_x64-git-be /usr/libexec/git-core /usr/libexec/git-core
COPY --from=centos6_x64-git-be /usr/share/git-core /usr/share/git-core
COPY --from=centos6_x64-gflags-be /usr/lib/libgflags.a /usr/lib/libgflags.la /usr/lib/libgflags.so.2.1.0 /usr/lib/libgflags_nothreads.a /usr/lib/libgflags_nothreads.la /usr/lib/libgflags_nothreads.so.2.1.0 /usr/lib/
COPY --from=centos6_x64-gflags-be /usr/include/gflags /usr/include/gflags
COPY --from=centos6_x64-gflags-be /usr/include/google /usr/include/google
COPY --from=centos6_x64-gflags-be /usr/lib/pkgconfig/libgflags* /usr/lib/pkgconfig/
RUN \
  pushd /usr/lib \
  && ln -s libgflags.so.2.1.0 libgflags.so \
  && ln -s libgflags.so.2.1.0 libgflags.so.2 \
  && ln -s libgflags_nothreads.so.2.1.0 libgflags_nothreads.so \
  && ln -s libgflags_nothreads.so.2.1.0 libgflags_nothreads.so.2 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install cmake3 \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake 10 --slave /usr/local/bin/ctest ctest /usr/bin/ctest --slave /usr/local/bin/cpack cpack /usr/bin/cpack --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake \
  && alternatives --install /usr/local/bin/cmake cmake /usr/bin/cmake3 20 --slave /usr/local/bin/ctest ctest /usr/bin/ctest3 --slave /usr/local/bin/cpack cpack /usr/bin/cpack3 --slave /usr/local/bin/ccmake ccmake /usr/bin/ccmake3 \
  && yum --setopt=tsflags=nodocs -y install bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && yum --setopt=tsflags=nodocs -y install java-1.8.0-openjdk-devel \
  && yum -y clean all

ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
