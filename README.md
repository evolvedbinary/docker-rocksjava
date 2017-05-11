# Docker images for cross-compiling RocksJava

https://hub.docker.com/r/evolvedbinary/rocksjava/

# CentOS 5 Docker Images
To build the CentOS 5 Docker images locally:

```bash
$ git clone https://github.com/evolvedbinary/docker-rocksjava
$ cd docker-rocksjava

$ cd centos5_x64
$ docker build --rm --force-rm --squash -t evolvedbinary/docker-rocksjava:centos5_x64 .

$ cd ../centos5_x86
$ docker build --rm --force-rm --squash -t evolvedbinary/docker-rocksjava:centos5_x86 .
```
