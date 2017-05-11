FROM toopher/centos-i386:centos5

MAINTAINER Evolved Binary Ltd <tech@evolvedbinary.com>

LABEL name="CentOS 5 (x86) RocksJava" \
  vendor="Evolved Binary Ltd"

RUN sed -i -e '/^mirrorlist=/d' -e 's%^#\sbaseurl=http://mirror.centos.org/centos/5/%baseurl=http://vault.centos.org/5.11/%g' /etc/yum.repos.d/CentOS-Base.repo \
  && sed -i -e '/^mirrorlist=/d' -e 's%^#\sbaseurl=http://mirror.centos.org/centos/5/%baseurl=http://vault.centos.org/5.11/%g' /etc/yum.repos.d/CentOS-fasttrack.repo \
  && linux32 yum -y update \
  && linux32 yum -y install curl \
  && linux32 yum -y install wget \
  && wget -O /etc/yum.repos.d/devtools-2.repo https://people.centos.org/tru/devtools-2/devtools-2.repo \
  && linux32 yum -y --enablerepo=testing-devtools-2-centos-5 install make devtoolset-2-binutils devtoolset-2-gcc devtoolset-2-gcc-c++ \
  && linux32 yum -y install epel-release \
  && linux32 yum -y install zlib-devel bzip2-devel lz4-devel snappy-devel \
  && linux32 yum -y install openssl java-1.7.0-openjdk-devel \
  && linux32 yum -y install which \
  && linux32 yum -y clean all \
  && rm -rf /usr/share/docs/* /usr/share/man/* \
  && wget https://github.com/facebook/zstd/archive/v1.1.3.tar.gz -O zstd-1.1.3.tar.gz \
  && tar zxvf zstd-1.1.3.tar.gz; cd zstd-1.1.3; linux32 scl enable devtoolset-2 make; linux32 scl enable devtoolset-2 'make install'; cd ..; rm -r zstd-1.1.3* \
  && wget https://github.com/gflags/gflags/archive/v2.0.tar.gz -O gflags-2.0.tar.gz \
  && tar xvfz gflags-2.0.tar.gz; cd gflags-2.0; linux32 scl enable devtoolset-2 ./configure; linux32 scl enable devtoolset-2 make; linux32 scl enable devtoolset-2 'make install'; cd ..; rm -r gflags-2.0*

# NOTE we manually remove /usr/share/docs and /usr/share/man, in CentOS 6+ we could use --setopt=tsflags=nodocs with YUM

ENV JAVA_HOME=/usr/lib/jvm/java-1.7.0
ENV PATH=$JAVA_HOME:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
