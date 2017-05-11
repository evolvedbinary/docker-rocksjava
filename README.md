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
$ docker build --rm --force-rm --squash -t evolvedbinary/rocksjava:centos5_x86-be .
```
