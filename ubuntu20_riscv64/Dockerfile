FROM riscv64/ubuntu:20.04

LABEL org.opencontainers.image.authors="Evolved Binary Ltd <tech@evolvedbinary.com>"

LABEL name="Ubuntu 20.04 (riscv64) RocksJava Build Environment" \
  vendor="Evolved Binary Ltd"

# version of Apache Maven to install
ARG MAVEN_VERSION=3.9.12

RUN apt-get -y update \
  && apt-get -y dist-upgrade \
  && apt-get -y install software-properties-common \
  && add-apt-repository -y ppa:ubuntu-toolchain-r/test \
  && apt-get -y update \
  && apt-get -y install cmake \
  && apt-get -y install gcc-11 g++-11 make perl \
  && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 100 \
  && update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 100 \
  && apt-get -y install libjemalloc-dev zlib1g-dev libbz2-dev liblz4-dev libsnappy-dev libzstd-dev libgflags-dev \
  && apt-get -y install openssl openjdk-11-jdk-headless wget curl \
  && wget https://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz \
  && tar zxvf apache-maven-$MAVEN_VERSION-bin.tar.gz -C /opt \
  && rm apache-maven-$MAVEN_VERSION-bin.tar.gz

ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-riscv64
ENV M2_HOME=/opt/apache-maven-$MAVEN_VERSION
ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
