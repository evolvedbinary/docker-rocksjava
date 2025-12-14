# Docker images for cross-compiling RocksJava

These Docker images simply provide a suitable build environment, they do not
include RocksDB or RocksJava itself. They could however also be used as base
images for a RocksDB runtime environment.

https://hub.docker.com/r/evolvedbinary/rocksjava/

## CentOS 5 Docker Images
To build the CentOS 5 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

### Requires x86_64 arch CPU
```
$ cd centos5_x64
$ docker build --platform linux/amd64 --rm --force-rm --squash -t evolvedbinary/rocksjava:centos5_x64-be .

$ cd ../centos5_x86
$ docker build --platform linux/386 --rm --force-rm --squash --platform linux/386 -t evolvedbinary/rocksjava:centos5_x86-be .
```

## CentOS 6 Docker Images
To build the CentOS 6 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

### Requires x86_64 arch CPU
```
$ cd centos6_x64
$ docker build --platform linux/amd64 --rm --force-rm --squash -t evolvedbinary/rocksjava:centos6_x64-be .

$ cd ../centos6_x86
$ docker build --platform linux/386 --rm --force-rm --squash --platform linux/386 -t evolvedbinary/rocksjava:centos6_x86-be .
```

## CentOS 7 Docker Images
To build the CentOS 7 Docker images locally:
```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

### Requires x86_64 arch CPU
```
$ cd centos7_x64
$ docker build --platform linux/amd64 --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_x64-be .

$ cd ../centos7_x86
$ docker build --platform linux/386 --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_x86-be .
```

### Requires ppc64le arch CPU
```
$ cd centos7_ppc64le
$ docker build --platform linux/ppc64le --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_ppc64le-be .
```

### Requires arm64v8 arch CPU
```
$ cd centos7_arm64v8
$ docker build --platform linux/aarch64 --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_arm64v8-be .
```

### Requires s390x arch CPU
```
$ cd centos7_s390x
$ docker build --platform linux/s390x --rm --force-rm --squash -t evolvedbinary/rocksjava:centos7_s390x-be .
```

## Ubuntu 20.04 Docker Images
To build the Ubuntu 20.04 Docker images locally:
```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

### Requires riscv64 arch CPU
```
$ cd ubuntu20_riscv64
$ docker build --platform linux/riscv64 --rm --force-rm --squash -t evolvedbinary/rocksjava:ubuntu20_riscv64-be .
```

## Ubuntu 22.04 Docker Images
To build the Ubuntu 22.04 Docker images locally:
```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

### Requires x86_64 arch CPU
```
$ cd ubuntu22_x64
$ docker build --platform linux/amd64 --rm --force-rm --squash -t evolvedbinary/rocksjava:ubuntu22_x64-be .
```

## Alpine 3.18 Docker Images
**NOTE**: Alpine uses *muslc* instead of *glibc*, and so RocksDB builds on Alpine will only run on systems with muslc.

To build the Alpine 3.18 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava
```

### Requires x86_64 arch CPU
```
$ cd alpine3_x64
$ docker build --platform linux/amd64 --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_x64-be .

$ cd ../alpine3_x86
$ docker build --platform linux/386 --rm --force-rm --squash --platform linux/386 -t evolvedbinary/rocksjava:alpine3_x86-be .
```

### Requires ppc64le arch CPU
```
$ cd alpine3_ppc64le
$ docker build --platform linux/ppc64le --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_ppc64le-be .
```

### Requires arm64v8 arch CPU
```
$ cd alpine3_arm64v8
$ docker build --platform linux/aarch64 --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_arm64v8-be .
```

### Requires s390x arch CPU
```
$ cd alpine3_s390x
$ docker build --platform linux/s390x --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_s390x-be .
```

### Requires riscv64 arch CPU
**NOTE** This actually uses Alpine 3.20 and OpenJDK 20 as those are the minimum versions that support RISCV on Alpine.
```
$ cd alpine3_riscv64
$ docker build --platform linux/riscv64 --rm --force-rm --squash -t evolvedbinary/rocksjava:alpine3_riscv64-be .
```

## Testing a Docker Image

For example to test the Docker CentOS 6 x64 build environment for RocksDB:

```bash
$ docker run --platform linux/amd64 --volume /local-path-to/rocksdb:/rocksdb-host -it evolvedbinary/rocksjava:centos6_x64-be /bin/bash
```

