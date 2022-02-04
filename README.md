# Docker images for cross-compiling RocksJava

These Docker images simply provide a suitable build environment, they do not
include RocksDB or RocksJava itself. They could however also be used as base
images for a RocksDB runtime environment.

https://hub.docker.com/r/evolvedbinary/rocksjava/

# CentOS 5 Docker Images
To build the CentOS 5 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava

$ cd centos5_x64
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:centos5_x64-be .

$ cd ../centos5_x86
$ docker build --rm --force-rm --squash -t --platform linux/386 evolvedbinary/rocksjava:centos5_x86-be .
```

# CentOS 6 Docker Images
To build the CentOS 6 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava

$ cd centos6_x64
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:centos6_x64-be .

$ cd ../centos6_x86
$ docker build --rm --force-rm --squash --platform linux/386 -t evolvedbinary/rocksjava:centos6_x86-be .
```

# CentOS 7 Docker Images
To build the CentOS 7 Docker images locally:
```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

## Requires ppc64le arch CPU
```
$ cd centos7_ppc64le
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_ppc64le-be .
```

## Requires arm64v8 arch CPU
```
$ cd centos7_arm64v8
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_arm64v8-be .
```

## Requires s390x arch CPU
```
$ cd centos7_s390x
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_s390x-be .
```

# Ubuntu 18.04 Docker Images
To build the Ubuntu 18.04 Docker images locally:
```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

## Requires s390x arch CPU
```
$ cd ubuntu18_s390x
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:ubuntu18_s390x-be .
```

# Alpine 3.10 Docker Images
**NOTE**: Alpine uses *muslc* instead of *glibc*, and so RocksDB builds on Alpine will only run on systems with muslc.

To build the Alpine 3.10 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava

$ cd alpine3_x64
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_x64-be .

$ cd ../alpine3_x86
$ docker build --rm --force-rm --squash -t --platform linux/386 evolvedbinary/rocksjava:alpine3_x86-be .
```

## Requires ppc64le arch CPU
```
$ cd alpine3_ppc64le
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_ppc64le-be .
```

## Requires arm64v8 arch CPU
```
$ cd alpine3_arm64v8
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_arm64v8-be .
```

## Requires s390x arch CPU
```
$ cd alpine3_s390x
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_s390x-be .
```

# Testing a Docker Image

For example to test the Docker CentOS 6 x64 build environment for RocksDB:

```bash
$ docker run --volume /local-path-to/rocksdb:/rocksdb-host -it evolvedbinary/rocksjava:centos6_x64-be /bin/bash
```

