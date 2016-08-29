FROM frolvlad/alpine-glibc:alpine-3.4

# update Alpine with latest versions
RUN echo '@edge http://nl.alpinelinux.org/alpine/edge/main' >> /etc/apk/repositories \
  && echo '@community http://nl.alpinelinux.org/alpine/edge/community' >> /etc/apk/repositories \
  && apk update \
  && apk upgrade

# install CA certificates
RUN apk add ca-certificates

# install GCC and build tools
RUN apk add build-base

# install tool dependencies for building RocksDB static library
RUN apk add perl \
  && apk add curl

# install RocksDB dependencies
RUN apk add snappy \
  && apk add zlib \
  && apk add bzip2 \
  && apk add lz4 \
  && apk add linux-headers

# install OpenJDK7
RUN apk add openjdk7 \
  && apk add java-cacerts \
  && rm /usr/lib/jvm/java-1.7-openjdk/jre/lib/security/cacerts \
  && ln -s /etc/ssl/certs/java/cacerts /usr/lib/jvm/java-1.7-openjdk/jre/lib/security/cacerts

# replace muslc JNI with OpenJDK JNI
RUN mv /usr/lib/gcc/x86_64-alpine-linux-musl/5.3.0/include/jni.h /usr/lib/gcc/x86_64-alpine-linux-musl/5.3.0/include/jni.h.BAK \
  && mv /usr/lib/gcc/x86_64-alpine-linux-musl/5.3.0/include/jni_md.h /usr/lib/gcc/x86_64-alpine-linux-musl/5.3.0/include/jni_md.h.BAK \
  && ln -s /usr/lib/jvm/java-1.7-openjdk/include/jni.h /usr/lib/gcc/x86_64-alpine-linux-musl/5.3.0/include/jni.h \
  && ln -s /usr/lib/jvm/java-1.7-openjdk/include/linux/jni_md.h  /usr/lib/gcc/x86_64-alpine-linux-musl/5.3.0/include/jni_md.h

# cleanup
RUN rm -rf /var/cache/apk/*

# puts javac in the PATH
ENV PATH=/usr/lib/jvm/java-1.7-openjdk/bin:$PATH
