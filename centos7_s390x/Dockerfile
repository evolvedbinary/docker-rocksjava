FROM s390x/clefos:7 AS centos7_s390x-base-be

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="CentOS 7 (s390x) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# make: Allow N jobs at once; infinite jobs with no arg
ARG JOBS=1

# version of openssl to compile
ARG OPENSSL_VERSION=4.0.1

# version of libssh2 to compile
ARG LIBSSH2_VERSION=1.11.1

# version of nghttp2 to compile
ARG NGHTTP2_VERSION=1.66.0

# version of libpsl to compile
ARG LIBPSL_VERSION=0.21.5

# version of curl to compile
ARG CURL_VERSION=8.20.0

# version of Git to compile
ARG GIT_VERSION=2.55.0

# version of Apache Maven to install
ARG MAVEN_VERSION=3.9.12

# version of cmake to compile
ARG CMAKE_VERSION_SHORT=3.31
ARG CMAKE_VERSION=3.31.12

# version of gflags to compile
ARG GFLAGS_VERSION=2.2.2


# 1. Build a base CentOS 7 Build Environment
COPY ClefOS-Base.repo /etc/yum.repos.d/ClefOS-Base.repo
COPY ClefOS-Extras.repo /etc/yum.repos.d/ClefOS-Extras.repo
COPY ClefOS-CentOSPlus.repo /etc/yum.repos.d/ClefOS-CentOSPlus.repo
RUN \
  yum --setopt=tsflags=nodocs -y update \
  && yum -y install centos-release-scl-rh \
  && yum --setopt=tsflags=nodocs -y upgrade \
  && yum --setopt=tsflags=nodocs -y install devtoolset-11-gcc devtoolset-11-gcc-c++ devtoolset-11-binutils devtoolset-11-runtime \
  && yum --setopt=tsflags=nodocs -y install make perl python3 which bzip2 wget \
  && yum --setopt=tsflags=nodocs -y install gettext-devel zlib-devel expat-devel jemalloc-devel \
  && yum -y clean all


# 2. Build a newer CMake (needed for libssh2, gflags, and RocksDB Build Environment)
FROM centos7_s390x-base-be AS centos7_s390x-cmake-be
RUN \
  yum install --setopt=tsflags=nodocs -y curl-devel \
  && wget -qO- https://cmake.org/files/v$CMAKE_VERSION_SHORT/cmake-$CMAKE_VERSION.tar.gz  | tar -zxvf- -C /tmp \
  && pushd /tmp/cmake-$CMAKE_VERSION \
  && scl enable devtoolset-11 'MAKEFLAGS=-j$JOBS ./bootstrap --system-curl' \
  && scl enable devtoolset-11 'make -j $JOBS' \
  && scl enable devtoolset-11 'make install' \
  && popd \
  && rm -rf /tmp/cmake-$CMAKE_VERSION


# 3. Buld a newer openssl (needed for libssh2 and libcurl)
FROM centos7_s390x-base-be AS centos7_s390x-openssl-be
RUN \
  yum --setopt=tsflags=nodocs -y install perl-IPC-Cmd perl-Time-Piece \
  && wget -qO- https://github.com/openssl/openssl/releases/download/openssl-$OPENSSL_VERSION/openssl-$OPENSSL_VERSION.tar.gz | tar -zxvf- -C /tmp \
  && pushd /tmp/openssl-$OPENSSL_VERSION \
  && scl enable devtoolset-11 './Configure --prefix=/usr/local --openssldir=/usr/local/ssl' \
  && scl enable devtoolset-11 'make -j $JOBS' \
  && scl enable devtoolset-11 'make install' \
  && popd \
  && rm -rf /tmp/openssl-$OPENSSL_VERSION


# 4. Buld a newer libssh2 (needed for libcurl)
FROM centos7_s390x-base-be AS centos7_s390x-libssh2-be
COPY --from=centos7_s390x-cmake-be /usr/local/bin/cmake /usr/local/bin/cmake
COPY --from=centos7_s390x-cmake-be /usr/local/bin/ctest /usr/local/bin/ctest
COPY --from=centos7_s390x-cmake-be /usr/local/bin/cpack /usr/local/bin/cpack
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/include /usr/local/share/cmake-3.31/include
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/Modules /usr/local/share/cmake-3.31/Modules
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/Templates /usr/local/share/cmake-3.31/Templates
COPY --from=centos7_s390x-cmake-be /usr/local/share/aclocal/cmake.m4 /usr/local/share/aclocal/cmake.m4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.a /usr/local/lib64/libcrypto.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.so.4 /usr/local/lib64/libcrypto.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libcrypto.pc /usr/local/lib64/pkgconfig/libcrypto.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.a /usr/local/lib64/libssl.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.so.4 /usr/local/lib64/libssl.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libssl.pc /usr/local/lib64/pkgconfig/libssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/openssl.pc /usr/local/lib64/pkgconfig/openssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/cmake/OpenSSL /usr/local/lib64/cmake/OpenSSL
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/ossl-modules/legacy.so /usr/local/lib64/ossl-modules/legacy.so
COPY --from=centos7_s390x-openssl-be /usr/local/include/openssl /usr/local/include/openssl
COPY --from=centos7_s390x-openssl-be /usr/local/ssl /usr/local/ssl
RUN \
  pushd /usr/local/lib64 \
  && ln -sf libcrypto.so.4 libcrypto.so \
  && ln -sf libssl.so.4 libssl.so \
  && ldconfig \
  && popd \
  && wget -qO- https://libssh2.org/download/libssh2-$LIBSSH2_VERSION.tar.bz2 | tar -jxvf- -C /tmp \
  && mkdir /tmp/libssh2-$LIBSSH2_VERSION/build \
  && pushd /tmp/libssh2-$LIBSSH2_VERSION/build \
  && scl enable devtoolset-11 'cmake -DCRYPTO_BACKEND=OpenSSL -DOPENSSL_ROOT_DIR=/usr/local -DCMAKE_INSTALL_PREFIX=/usr/local ..' \
  && scl enable devtoolset-11 'CMAKE_BUILD_PARALLEL_LEVEL=$JOBS cmake --build .' \
  && scl enable devtoolset-11 'cmake --build . --target=install' \
  && popd \
  && rm -rf /tmp/libssh2-$LIBSSH2_VERSION


# 5. Build a newer nghttp (needed for libcurl)
FROM centos7_s390x-base-be AS centos7_s390x-nghttp2-be
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.a /usr/local/lib64/libcrypto.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.so.4 /usr/local/lib64/libcrypto.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libcrypto.pc /usr/local/lib64/pkgconfig/libcrypto.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.a /usr/local/lib64/libssl.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.so.4 /usr/local/lib64/libssl.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libssl.pc /usr/local/lib64/pkgconfig/libssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/openssl.pc /usr/local/lib64/pkgconfig/openssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/cmake/OpenSSL /usr/local/lib64/cmake/OpenSSL
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/ossl-modules/legacy.so /usr/local/lib64/ossl-modules/legacy.so
COPY --from=centos7_s390x-openssl-be /usr/local/include/openssl /usr/local/include/openssl
COPY --from=centos7_s390x-openssl-be /usr/local/ssl /usr/local/ssl
RUN \
  pushd /usr/local/lib64 \
  && ln -sf libcrypto.so.4 libcrypto.so \
  && ln -sf libssl.so.4 libssl.so \
  && ldconfig \
  && popd \
  && wget -qO- https://github.com/nghttp2/nghttp2/releases/download/v$NGHTTP2_VERSION/nghttp2-$NGHTTP2_VERSION.tar.gz | tar -zxvf- -C /tmp \
  && pushd /tmp/nghttp2-$NGHTTP2_VERSION \
  && scl enable devtoolset-11 'PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig:$PKG_CONFIG_PATH ./configure --prefix=/usr/local --with-openssl=/usr/local --with-zlib --with-jemalloc --enable-lib-only' \
  && scl enable devtoolset-11 'make -j $JOBS' \
  && scl enable devtoolset-11 'make install' \
  && popd \
  && rm -rf /tmp/nghttp2-$NGHTTP2_VERSION


# 6. build a newer libpsl (needed for libcurl)
FROM centos7_s390x-base-be AS centos7_s390x-libpsl-be
RUN \
  wget -qO- https://github.com/rockdaboot/libpsl/releases/download/$LIBPSL_VERSION/libpsl-$LIBPSL_VERSION.tar.gz | tar -zxvf- -C /tmp \
  && pushd /tmp/libpsl-$LIBPSL_VERSION \
  && scl enable devtoolset-11 './configure --prefix=/usr/local' \
  && scl enable devtoolset-11 'make -j $JOBS' \
  && scl enable devtoolset-11 'make install' \
  && popd \
  && rm -rf /tmp/libpsl-$LIBPSL_VERSION


# 7. Build a newer libcurl (needed for git)
FROM centos7_s390x-base-be AS centos7_s390x-curl-be
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.a /usr/local/lib64/libcrypto.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.so.4 /usr/local/lib64/libcrypto.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libcrypto.pc /usr/local/lib64/pkgconfig/libcrypto.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.a /usr/local/lib64/libssl.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.so.4 /usr/local/lib64/libssl.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libssl.pc /usr/local/lib64/pkgconfig/libssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/openssl.pc /usr/local/lib64/pkgconfig/openssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/cmake/OpenSSL /usr/local/lib64/cmake/OpenSSL
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/ossl-modules/legacy.so /usr/local/lib64/ossl-modules/legacy.so
COPY --from=centos7_s390x-openssl-be /usr/local/include/openssl /usr/local/include/openssl
COPY --from=centos7_s390x-openssl-be /usr/local/ssl /usr/local/ssl
COPY --from=centos7_s390x-libssh2-be /usr/local/include/libssh2.h /usr/local/include/libssh2.h
COPY --from=centos7_s390x-libssh2-be /usr/local/include/libssh2_publickey.h /usr/local/include/libssh2_publickey.h
COPY --from=centos7_s390x-libssh2-be /usr/local/include/libssh2_sftp.h /usr/local/include/libssh2_sftp.h
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/libssh2.a /usr/local/lib64/libssh2.a
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/libssh2.so.1.0.1 /usr/local/lib64/libssh2.so.1.0.1
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/cmake/libssh2 /usr/local/lib64/cmake/libssh2
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/pkgconfig/libssh2.pc /usr/local/lib64/pkgconfig/libssh2.pc
COPY --from=centos7_s390x-nghttp2-be /usr/local/include/nghttp2 /usr/local/include/nghttp2
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.a /usr/local/lib/libnghttp2.a
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.so.14.28.5 /usr/local/lib/libnghttp2.so.14.28.5
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.la /usr/local/lib/libnghttp2.la
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/pkgconfig/libnghttp2.pc /usr/local/lib/pkgconfig/libnghttp2.pc
#COPY --from=centos7_s390x-nghttp2-be /usr/local/share/nghttp2 /usr/local/share/nghttp2
COPY --from=centos7_s390x-libpsl-be /usr/local/include/libpsl.h /usr/local/include/libpsl.h
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.a /usr/local/lib/libpsl.a
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.so.5.3.5 /usr/local/lib/libpsl.so.5.3.5
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.la /usr/local/lib/libpsl.la
RUN \
  pushd /usr/local/lib \
  && ln -sf libcrypto.so.4 libcrypto.so \
  && ln -sf libssl.so.4 libssl.so \
  && ln -sf libssh2.so.1.0.1 libssh2.so.1 \
  && ln -sf libssh2.so.1 libssh2.so \
  && ln -sf libnghttp2.so.14.28.5 libnghttp2.so \
  && ln -sf libnghttp2.so.14.28.5 libnghttp2.so.14 \
  && ln -sf libpsl.so.5.3.5 libpsl.so \
  && ln -sf libpsl.so.5.3.5 libpsl.so.5 \
  && ldconfig \
  && popd \
  && pushd /usr/local/lib64 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install libev-devel libpsl \
  && wget -qO- https://curl.haxx.se/download/curl-$CURL_VERSION.tar.gz | tar -zxvf- -C /tmp \
  && pushd /tmp/curl-$CURL_VERSION \
  && scl enable devtoolset-11 './configure --prefix=/usr/local --enable-ipv6 --enable-unix-sockets --with-openssl=/usr/local --with-libssh2=/usr/local LDFLAGS="-Wl,-rpath,/usr/local/lib64"' \
  && scl enable devtoolset-11 'make -j $JOBS' \
  && scl enable devtoolset-11 'make install' \
  && popd \
  && rm -rf /tmp/curl-$CURL_VERSION


# 8. Build a newer git (needed for GitHub Actions to use `git --progress` in its `checkout` step)
FROM centos7_s390x-base-be AS centos7_s390x-git-be
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.a /usr/local/lib64/libcrypto.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.so.4 /usr/local/lib64/libcrypto.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libcrypto.pc /usr/local/lib64/pkgconfig/libcrypto.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.a /usr/local/lib64/libssl.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.so.4 /usr/local/lib64/libssl.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libssl.pc /usr/local/lib64/pkgconfig/libssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/openssl.pc /usr/local/lib64/pkgconfig/openssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/cmake/OpenSSL /usr/local/lib64/cmake/OpenSSL
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/ossl-modules/legacy.so /usr/local/lib64/ossl-modules/legacy.so
COPY --from=centos7_s390x-openssl-be /usr/local/include/openssl /usr/local/include/openssl
COPY --from=centos7_s390x-openssl-be /usr/local/ssl /usr/local/ssl
COPY --from=centos7_s390x-nghttp2-be /usr/local/include/nghttp2 /usr/local/include/nghttp2
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.a /usr/local/lib/libnghttp2.a 
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.so.14.28.5 /usr/local/lib/libnghttp2.so.14.28.5
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.la /usr/local/lib/libnghttp2.la
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/pkgconfig/libnghttp2.pc /usr/local/lib/pkgconfig/libnghttp2.pc
#COPY --from=centos7_s390x-nghttp2-be /usr/local/share/nghttp2 /usr/local/share/nghttp2
COPY --from=centos7_s390x-libpsl-be /usr/local/include/libpsl.h /usr/local/include/libpsl.h
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.a /usr/local/lib/libpsl.a
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.so.5.3.5 /usr/local/lib/libpsl.so.5.3.5
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.la /usr/local/lib/libpsl.la
COPY --from=centos7_s390x-curl-be /usr/local/bin/curl /usr/local/bin/curl
COPY --from=centos7_s390x-curl-be /usr/local/bin/curl-config /usr/local/bin/curl-config
COPY --from=centos7_s390x-curl-be /usr/local/include/curl /usr/local/include/curl
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.a /usr/local/lib/libcurl.a
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.la /usr/local/lib/libcurl.la
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.so.4.8.0 /usr/local/lib/libcurl.so.4.8.0
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.so.4.8.0 /usr/local/lib64/libcurl.so.4.8.0
#COPY --from=centos7_s390x-curl-be /usr/local/lib64/libcurl.so.4.8.0 /usr/local/lib64/libcurl.so.4.8.0
COPY --from=centos7_s390x-curl-be /usr/local/lib/pkgconfig/libcurl.pc /usr/local/lib/pkgconfig/libcurl.pc
RUN \
  pushd /usr/local/lib \
  && ln -sf libcrypto.so.4 libcrypto.so \
  && ln -sf libssl.so.4 libssl.so \
  && ln -sf libnghttp2.so.14.28.5 libnghttp2.so \
  && ln -sf libnghttp2.so.14.28.5 libnghttp2.so.14 \
  && ln -sf libpsl.so.5.3.5 libpsl.so \
  && ln -sf libpsl.so.5.3.5 libpsl.so.5 \
  && ln -sf libcurl.so.4.8.0 libcurl.so \
  && ln -sf libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && pushd /usr/local/lib64 \
  && ln -sf libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && yum --setopt=tsflags=nodocs -y install autoconf cargo \
  && wget -qO- https://mirrors.edge.kernel.org/pub/software/scm/git/git-$GIT_VERSION.tar.gz | tar -zxvf- -C /tmp \
  && pushd /tmp/git-$GIT_VERSION \
  && scl enable devtoolset-11 'make configure' \
  && scl enable devtoolset-11 'NO_PERL=YesPlease NO_TCLTK=YesPlease NO_GETTEXT=YesPlease ./configure --prefix=/usr/local --with-openssl=/usr/local LDFLAGS="-Wl,-rpath,/usr/local/lib64"' \
  && echo "CSPRNG_METHOD = urandom" >> config.mak \
  && scl enable devtoolset-11 'make -j $JOBS all' \
  && scl enable devtoolset-11 'make install' \
  && popd \
  && rm -rf /tmp/git-$GIT_VERSION

# 9. Build a newer gflags (needed for RocksDB Build Environment)
FROM centos7_s390x-base-be AS centos7_s390x-gflags-be
COPY --from=centos7_s390x-cmake-be /usr/local/bin/cmake /usr/local/bin/cmake
COPY --from=centos7_s390x-cmake-be /usr/local/bin/ctest /usr/local/bin/ctest
COPY --from=centos7_s390x-cmake-be /usr/local/bin/cpack /usr/local/bin/cpack
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/include /usr/local/share/cmake-3.31/include
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/Modules /usr/local/share/cmake-3.31/Modules
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/Templates /usr/local/share/cmake-3.31/Templates
COPY --from=centos7_s390x-cmake-be /usr/local/share/aclocal/cmake.m4 /usr/local/share/aclocal/cmake.m4
RUN \
  pushd /usr/local/lib64 \
  && ldconfig \
  && popd \
  && wget -qO- https://github.com/gflags/gflags/archive/refs/tags/v$GFLAGS_VERSION.tar.gz | tar -zxvf- -C /tmp \
  && mkdir /tmp/gflags-$GFLAGS_VERSION/build \
  && pushd /tmp/gflags-$GFLAGS_VERSION/build \
  && scl enable devtoolset-11 'CMAKE_INSTALL_PREFIX=/usr/local cmake ..' \
  && scl enable devtoolset-11 'CMAKE_BUILD_PARALLEL_LEVEL=$JOBS cmake --build .' \
  && scl enable devtoolset-11 'cmake --build . --target=install' \
  && popd \
  && rm -rf /tmp/gflags-$GFLAGS_VERSION


# 10. Finally build the RocksDB Build Environment
FROM centos7_s390x-base-be AS centos7_s390x-rocksdb-be
COPY --from=centos7_s390x-cmake-be /usr/local/bin/cmake /usr/local/bin/cmake
COPY --from=centos7_s390x-cmake-be /usr/local/bin/ctest /usr/local/bin/ctest
COPY --from=centos7_s390x-cmake-be /usr/local/bin/cpack /usr/local/bin/cpack
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/include /usr/local/share/cmake-3.31/include
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/Modules /usr/local/share/cmake-3.31/Modules
COPY --from=centos7_s390x-cmake-be /usr/local/share/cmake-3.31/Templates /usr/local/share/cmake-3.31/Templates
COPY --from=centos7_s390x-cmake-be /usr/local/share/aclocal/cmake.m4 /usr/local/share/aclocal/cmake.m4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.a /usr/local/lib64/libcrypto.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libcrypto.so.4 /usr/local/lib64/libcrypto.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libcrypto.pc /usr/local/lib64/pkgconfig/libcrypto.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.a /usr/local/lib64/libssl.a
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/libssl.so.4 /usr/local/lib64/libssl.so.4
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/libssl.pc /usr/local/lib64/pkgconfig/libssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/pkgconfig/openssl.pc /usr/local/lib64/pkgconfig/openssl.pc
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/cmake/OpenSSL /usr/local/lib64/cmake/OpenSSL
COPY --from=centos7_s390x-openssl-be /usr/local/lib64/ossl-modules/legacy.so /usr/local/lib64/ossl-modules/legacy.so
COPY --from=centos7_s390x-openssl-be /usr/local/include/openssl /usr/local/include/openssl
COPY --from=centos7_s390x-openssl-be /usr/local/ssl /usr/local/ssl
COPY --from=centos7_s390x-git-be /usr/local/bin/git /usr/local/bin/git
COPY --from=centos7_s390x-git-be /usr/local/libexec/git-core /usr/local/libexec/git-core
COPY --from=centos7_s390x-git-be /usr/local/share/git-core /usr/local/share/git-core
COPY --from=centos7_s390x-nghttp2-be /usr/local/include/nghttp2 /usr/local/include/nghttp2
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.a /usr/local/lib/libnghttp2.a 
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.so.14.28.5 /usr/local/lib/libnghttp2.so.14.28.5
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/libnghttp2.la /usr/local/lib/libnghttp2.la
COPY --from=centos7_s390x-nghttp2-be /usr/local/lib/pkgconfig/libnghttp2.pc /usr/local/lib/pkgconfig/libnghttp2.pc
#COPY --from=centos7_s390x-nghttp2-be /usr/local/share/nghttp2 /usr/local/share/nghttp2
COPY --from=centos7_s390x-libssh2-be /usr/local/include/libssh2.h /usr/local/include/libssh2.h
COPY --from=centos7_s390x-libssh2-be /usr/local/include/libssh2_publickey.h /usr/local/include/libssh2_publickey.h
COPY --from=centos7_s390x-libssh2-be /usr/local/include/libssh2_sftp.h /usr/local/include/libssh2_sftp.h
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/libssh2.a /usr/local/lib64/libssh2.a
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/libssh2.so.1.0.1 /usr/local/lib64/libssh2.so.1.0.1
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/cmake/libssh2 /usr/local/lib64/cmake/libssh2
COPY --from=centos7_s390x-libssh2-be /usr/local/lib64/pkgconfig/libssh2.pc /usr/local/lib64/pkgconfig/libssh2.pc
COPY --from=centos7_s390x-libpsl-be /usr/local/include/libpsl.h /usr/local/include/libpsl.h
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.a /usr/local/lib/libpsl.a
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.so.5.3.5 /usr/local/lib/libpsl.so.5.3.5
COPY --from=centos7_s390x-libpsl-be /usr/local/lib/libpsl.la /usr/local/lib/libpsl.la
COPY --from=centos7_s390x-curl-be /usr/local/bin/curl /usr/local/bin/curl
COPY --from=centos7_s390x-curl-be /usr/local/bin/curl-config /usr/local/bin/curl-config
COPY --from=centos7_s390x-curl-be /usr/local/include/curl /usr/local/include/curl
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.a /usr/local/lib/libcurl.a
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.la /usr/local/lib/libcurl.la
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.so.4.8.0 /usr/local/lib/libcurl.so.4.8.0
COPY --from=centos7_s390x-curl-be /usr/local/lib/libcurl.so.4.8.0 /usr/local/lib64/libcurl.so.4.8.0
#COPY --from=centos7_s390x-curl-be /usr/local/lib64/libcurl.so.4.8.0 /usr/local/lib64/libcurl.so.4.8.0
COPY --from=centos7_s390x-curl-be /usr/local/lib/pkgconfig/libcurl.pc /usr/local/lib/pkgconfig/libcurl.pc
COPY --from=centos7_s390x-gflags-be /usr/local/lib/libgflags.a /usr/local/lib/libgflags.a
COPY --from=centos7_s390x-gflags-be /usr/local/lib/libgflags_nothreads.a /usr/local/lib/libgflags_nothreads.a
COPY --from=centos7_s390x-gflags-be /usr/local/include/gflags /usr/local/include/gflags
COPY --from=centos7_s390x-gflags-be /usr/local/lib/cmake/gflags /usr/local/lib/cmake/gflags
COPY --from=centos7_s390x-gflags-be /usr/local/lib/pkgconfig/gflags.pc /usr/local/lib/pkgconfig/gflags.pc
RUN \
  pushd /usr/local/lib \
  && ln -sf libcrypto.so.4 libcrypto.so \
  && ln -sf libssl.so.4 libssl.so \
  && ln -sf libssh2.so.1.0.1 libssh2.so.1 \
  && ln -sf libssh2.so.1 libssh2.so \
  && ln -sf libnghttp2.so.14.28.5 libnghttp2.so \
  && ln -sf libnghttp2.so.14.28.5 libnghttp2.so.14 \
  && ln -sf libpsl.so.5.3.5 libpsl.so \
  && ln -sf libpsl.so.5.3.5 libpsl.so.5 \
  && ln -sf libcurl.so.4.8.0 libcurl.so \
  && ln -sf libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && pushd /usr/local/lib64 \
  && ln -sf libcurl.so.4.8.0 libcurl.so.4 \
  && ldconfig \
  && popd \
  && echo "/usr/local/lib64" > /etc/ld.so.conf.d/local.conf \
  && echo "/usr/local/lib" >> /etc/ld.so.conf.d/local.conf \
  && ldconfig \
  && yum --setopt=tsflags=nodocs -y install bzip2-devel lz4-devel snappy-devel libzstd-devel \
  && yum --setopt=tsflags=nodocs -y install java-1.8.0-openjdk-devel \
  && yum -y clean all \
  && wget -qO- https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar -zxvf- -C /opt


ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0
ENV M2_HOME=/opt/apache-maven-$MAVEN_VERSION
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
