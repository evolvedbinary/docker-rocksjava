FROM ppc64le/centos:7 AS centos7_ppc64le-base-be

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="CentOS 7 (ppc64le) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# make: Allow N jobs at once; infinite jobs with no arg
ARG JOBS=1

# version of nghttp2 to compile
ARG NGHTTP2_VERSION=1.66.0

# version of libpsl to compile
ARG LIBPSL_VERSION=0.21.5

# version of curl to compile
ARG CURL_VERSION=8.14.1

# version of Git to compile
ARG GIT_VERSION=2.48.1

# version of cmake to compile
ARG CMAKE_VERSION_SHORT=3.31
ARG CMAKE_VERSION=3.31.8

# version of zstd to compile
ARG ZSTD_VERSION=1.5.7

# version of gflags to compile
ARG GFLAGS_VERSION=2.2.2


# 1. Build a base CentOS 7 Build Environment
RUN \
  sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/altarch/\$releasever/%baseurl=https://vault.centos.org/altarch/7.9.2009/%g /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/altarch/\$releasever/%baseurl=https://vault.centos.org/altarch/7.9.2009/%g /etc/yum.repos.d/CentOS-fasttrack.repo \
  && yum --setopt=tsflags=nodocs -y update \
  && yum --setopt=tsflags=nodocs -y install epel-release \
  && yum --setopt=tsflags=nodocs -y install centos-release-scl \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-SCLo-scl.repo \
  && sed -i s%mirrorlist=http%#mirrorlist=http%g /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
  && sed -i s%#\\sbaseurl=http://mirror.centos.org/centos/7/%baseurl=https://vault.centos.org/altarch/7.9.2009/%g /etc/yum.repos.d/CentOS-SCLo-scl.repo \
  && sed -i s%#baseurl=http://mirror.centos.org/centos/7/%baseurl=https://vault.centos.org/altarch/7.9.2009/%g /etc/yum.repos.d/CentOS-SCLo-scl-rh.repo \
  && yum --setopt=tsflags=nodocs -y upgrade \
  && yum --setopt=tsflags=nodocs -y install devtoolset-7 \
  && yum --setopt=tsflags=nodocs -y install make perl which bzip2 wget \
  && yum --setopt=tsflags=nodocs -y install gettext-devel openssl openssl-devel zlib-devel expat-devel libssh2-devel jemalloc-devel  \
  && yum -y clean all


# 2. Build a newer nghttp (needed for libcurl)
FROM centos7_ppc64le-base-be AS centos7_ppc64le-nghttp2-be
RUN \
  wget https://github.com/nghttp2/nghttp2/releases/download/v$NGHTTP2_VERSION/nghttp2-$NGHTTP2_VERSION.tar.gz \
  && tar zxf nghttp2-$NGHTTP2_VERSION.tar.gz \
  && pushd nghttp2-$NGHTTP2_VERSION \
  && scl enable devtoolset-7 './configure --prefix=/usr --with-openssl --with-zlib --with-jemalloc --enable-lib-only' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install' \
  && popd \
  && rm -rf nghttp2-$NGHTTP2_VERSION nghttp2-$NGHTTP2_VERSION.tar.gz


# 3. build a newer libpsl (needed for libcurl)
FROM centos7_ppc64le-base-be AS centos7_ppc64le-libpsl-be
RUN \
  wget https://github.com/rockdaboot/libpsl/releases/download/$LIBPSL_VERSION/libpsl-$LIBPSL_VERSION.tar.gz \
  && tar zxf libpsl-$LIBPSL_VERSION.tar.gz \
  && pushd libpsl-$LIBPSL_VERSION \
  && scl enable devtoolset-7 './configure --prefix=/usr' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install' \
  && popd \
  && rm -rf libpsl-$LIBPSL_VERSION libpsl-$LIBPSL_VERSION.tar.gz


# 4. Build a newer libcurl (needed for git)
FROM centos7_ppc64le-base-be AS centos7_ppc64le-curl-be
COPY --from=centos7_ppc64le-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.so.14.28.5 /usr/lib/libnghttp2.so.14.28.5
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
#COPY --from=centos7_ppc64le-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
COPY --from=centos7_ppc64le-libpsl-be /usr/include/libpsl.h /usr/include/libpsl.h
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.a /usr/lib/libpsl.a
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.so.5.3.5 /usr/lib/libpsl.so.5.3.5
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.la /usr/lib/libpsl.a
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.28.5 libnghttp2.so \
  && ln -s libnghttp2.so.14.28.5 libnghttp2.so.14 \
  && ln -s libpsl.so.5.3.5 libpsl.so \
  && ln -s libpsl.so.5.3.5 libpsl.so.5 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install libev-devel libpsl \
  && wget https://curl.haxx.se/download/curl-$CURL_VERSION.tar.gz \
  && tar zxf curl-$CURL_VERSION.tar.gz \
  && pushd curl-$CURL_VERSION \
  && scl enable devtoolset-7 './configure --prefix=/usr --enable-ipv6 --enable-unix-sockets --with-ssl --with-libssh2' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install' \
  && popd \
  && rm -rf curl-$CURL_VERSION curl-$CURL_VERSION.tar.gz


# 5. Build a newer git (needed for GitHub Actions to use `git --progress` in its `checkout` step)
FROM centos7_ppc64le-base-be AS centos7_ppc64le-git-be
COPY --from=centos7_ppc64le-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a 
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.so.14.28.5 /usr/lib/libnghttp2.so.14.28.5
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
#COPY --from=centos7_ppc64le-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
COPY --from=centos7_ppc64le-libpsl-be /usr/include/libpsl.h /usr/include/libpsl.h
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.a /usr/lib/libpsl.a
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.so.5.3.5 /usr/lib/libpsl.so.5.3.5
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.la /usr/lib/libpsl.a
COPY --from=centos7_ppc64le-curl-be /usr/bin/curl /usr/bin/curl
COPY --from=centos7_ppc64le-curl-be /usr/bin/curl-config /usr/bin/curl-config
COPY --from=centos7_ppc64le-curl-be /usr/include/curl /usr/include/curl
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.a /usr/lib/libcurl.a
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.la /usr/lib/libcurl.la
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.so.4.8.0 /usr/lib/libcurl.so.4.8.0
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.so.4.8.0 /usr/lib64/libcurl.so.4.8.0
#COPY --from=centos7_ppc64le-curl-be /usr/lib64/libcurl.so.4.8.0 /usr/lib64/libcurl.so.4.8.0
COPY --from=centos7_ppc64le-curl-be /usr/lib/pkgconfig/libcurl.pc /usr/lib/pkgconfig/libcurl.pc
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.28.5 libnghttp2.so \
  && ln -s libnghttp2.so.14.28.5 libnghttp2.so.14 \
  && ln -s libpsl.so.5.3.5 libpsl.so \
  && ln -s libpsl.so.5.3.5 libpsl.so.5 \
  && ln -s libcurl.so.4.8.0 libcurl.so \
  && ln -s libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && pushd /usr/lib64 \
  && unlink libcurl.so.4 \
  && rm libcurl.so.4.3.0 \
  && ln -s libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install autoconf \
  && wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-$GIT_VERSION.tar.gz \
  && tar zxf git-$GIT_VERSION.tar.gz \
  && pushd git-$GIT_VERSION \
  && scl enable devtoolset-7 'make configure' \
  && scl enable devtoolset-7 'NO_PERL=YesPlease NO_TCLTK=YesPlease NO_GETTEXT=YesPlease ./configure --prefix=/usr' \
  && scl enable devtoolset-7 'make -j $JOBS all' \
  && scl enable devtoolset-7 'make install' \
  && popd \
  && rm -rf git-$GIT_VERSION git-$GIT_VERSION.tar.gz


# 6. Finally build the RocksDB Build Environment
FROM centos7_ppc64le-base-be AS centos7_ppc64le-rocksdb-be
COPY bellsoft.repo /etc/yum.repos.d/bellsoft.repo
COPY --from=centos7_ppc64le-git-be /usr/bin/git /usr/bin/git
COPY --from=centos7_ppc64le-git-be /usr/libexec/git-core /usr/libexec/git-core
COPY --from=centos7_ppc64le-git-be /usr/share/git-core /usr/share/git-core
COPY --from=centos7_ppc64le-nghttp2-be /usr/include/nghttp2 /usr/include/nghttp2
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.a /usr/lib/libnghttp2.a 
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.so.14.28.5 /usr/lib/libnghttp2.so.14.28.5
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/libnghttp2.la /usr/lib/libnghttp2.la
COPY --from=centos7_ppc64le-nghttp2-be /usr/lib/pkgconfig/libnghttp2.pc /usr/lib/pkgconfig/libnghttp2.pc
#COPY --from=centos7_ppc64le-nghttp2-be /usr/share/nghttp2 /usr/share/nghttp2
COPY --from=centos7_ppc64le-libpsl-be /usr/include/libpsl.h /usr/include/libpsl.h
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.a /usr/lib/libpsl.a
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.so.5.3.5 /usr/lib/libpsl.so.5.3.5
COPY --from=centos7_ppc64le-libpsl-be /usr/lib/libpsl.la /usr/lib/libpsl.a
COPY --from=centos7_ppc64le-curl-be /usr/bin/curl /usr/bin/curl
COPY --from=centos7_ppc64le-curl-be /usr/bin/curl-config /usr/bin/curl-config
COPY --from=centos7_ppc64le-curl-be /usr/include/curl /usr/include/curl
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.a /usr/lib/libcurl.a
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.la /usr/lib/libcurl.la
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.so.4.8.0 /usr/lib/libcurl.so.4.8.0
COPY --from=centos7_ppc64le-curl-be /usr/lib/libcurl.so.4.8.0 /usr/lib64/libcurl.so.4.8.0
#COPY --from=centos7_ppc64le-curl-be /usr/lib64/libcurl.so.4.8.0 /usr/lib64/libcurl.so.4.8.0
COPY --from=centos7_ppc64le-curl-be /usr/lib/pkgconfig/libcurl.pc /usr/lib/pkgconfig/libcurl.pc
RUN \
  pushd /usr/lib \
  && ln -s libnghttp2.so.14.28.5 libnghttp2.so \
  && ln -s libnghttp2.so.14.28.5 libnghttp2.so.14 \
  && ln -s libpsl.so.5.3.5 libpsl.so \
  && ln -s libpsl.so.5.3.5 libpsl.so.5 \
  && ln -s libcurl.so.4.8.0 libcurl.so \
  && ln -s libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && pushd /usr/lib64 \
  && unlink libcurl.so.4 \
  && rm libcurl.so.4.3.0 \
  && ln -s libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install bzip2-devel lz4-devel snappy-devel \
  && yum --setopt=tsflags=nodocs -y install bellsoft-java8 \
  && yum -y clean all \
  && wget https://cmake.org/files/v$CMAKE_VERSION_SHORT/cmake-$CMAKE_VERSION.tar.gz \
  && tar zxvf cmake-$CMAKE_VERSION.tar.gz \
  && pushd cmake-$CMAKE_VERSION \
  && scl enable devtoolset-7 './bootstrap --system-curl' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install' \
  && popd \
  && rm -rf cmake-$CMAKE_VERSION cmake-$CMAKE_VERSION.tar.gz \
  && wget https://github.com/facebook/zstd/releases/download/v$ZSTD_VERSION/zstd-$ZSTD_VERSION.tar.gz \
  && tar zxvf zstd-$ZSTD_VERSION.tar.gz \
  && pushd zstd-$ZSTD_VERSION \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make prefix=/usr install' \
  && popd \
  && rm -rf zstd-$ZSTD_VERSION zstd-$ZSTD_VERSION.tar.gz \
  && wget -O gflags-$GFLAGS_VERSION.tar.gz https://github.com/gflags/gflags/archive/refs/tags/v$GFLAGS_VERSION.tar.gz \
  && tar zxvf gflags-$GFLAGS_VERSION.tar.gz \
  && mkdir gflags-$GFLAGS_VERSION/build \
  && pushd gflags-$GFLAGS_VERSION/build \
  && scl enable devtoolset-7 'CMAKE_INSTALL_PREFIX=/usr cmake ..' \
  && scl enable devtoolset-7 'make -j $JOBS' \
  && scl enable devtoolset-7 'make install' \
  && popd \
  && rm -rf gflags-$GFLAGS_VERSION gflags-$GFLAGS_VERSION.tar.gz


ENV JAVA_HOME=/usr/lib/jvm/bellsoft-java8.ppc64le
ENV PATH=$JAVA_HOME/bin:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
