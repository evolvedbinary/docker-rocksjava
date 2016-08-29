FROM tatsushid/tinycore:7.2-x86_64

# install bash and GCC etc
RUN tce-load -wic bash compiletc glibc_apps

# install tool dependencies for building RocksDB static library
RUN tce-load -wic perl5 curl

# install RocksDB dependencies
RUN tce-load -wic zlib_base-dev bzip2-dev lz4 linux-4.2.1_api_headers

# TODO what about snappy?

# We use OpenJDK 7 from https://github.com/alexkasko/openjdk-unofficial-builds
# when we want to switch to OpenJDK 8 we can use https://github.com/ojdkbuild/ojdkbuild

# Download Open JDK 7 and convert it
# to look like Oracle JDK so we can use
# it with java-installer
RUN cd /tmp \
  && curl -O -L https://bitbucket.org/alexkasko/openjdk-unofficial-builds/downloads/openjdk-1.7.0-u80-unofficial-linux-amd64-image.zip \
  && unzip openjdk-1.7.0-u80-unofficial-linux-amd64-image.zip \
  && mv openjdk-1.7.0-u80-unofficial-linux-amd64-image jdk1.7.0_80 \
  && tar cvzf jdk-7u80-linux-x64.tar.gz jdk1.7.0_80 \
  && rm openjdk-1.7.0-u80-unofficial-linux-amd64-image.zip \
  && sudo mv jdk-7u80-linux-x64.tar.gz /

# install Open JDK 7
RUN tce-load -wic java-installer \
  && echo | sudo java-installer \
  && sudo echo "jdk7.tcz" >> /etc/sysconfig/tcedir/onboot.lst \
  && mv /tmp/tce/optional/jdk7.tcz* /tmp \
  && mv /tmp/tce/optional/jre7.tcz* /tmp \
  && tce-load -ic /tmp/jdk7.tcz \
  && sudo rm /tmp/jdk7.tcz* \
  && sudo rm /tmp/jre7.tcz* \
  && sudo rm /jdk-7u80-linux-x64.tar.gz


# puts javac in the PATH
ENV PATH=/usr/local/java/bin:$PATH
ENV JAVA_HOME=/usr/local/java
