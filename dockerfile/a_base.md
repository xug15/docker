# Base
* [Back to home](../README.md)

**Dockerfile**
```bash
FROM dongzhuoer/ubuntu-cn:rolling

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

## Set a default user 
RUN useradd test -m -s /bin/bash

# Thanks to https://github.com/tianon/docker-brew-ubuntu-core/issues/122#issuecomment-380529430
# Do not exclude man pages & other documentation
RUN rm /etc/dpkg/dpkg.cfg.d/excludes
# Reinstall all currently installed packages in order to get the man pages back (just reinstall packages used in lulab/teaching doesn't work, `coreutils util-linux debianutils procps gzip tar grep sed less` )
RUN apt update \
 && dpkg -l | grep ^ii | cut -d' ' -f3 | xargs apt-get install -y --reinstall \
 && rm -r /var/lib/apt/lists/*

# install utility programs
RUN apt update \
 && apt -y install less tree locate man wget vim \
 && rm -r /var/lib/apt/lists/
```

## bash
```sh
docker build -t xug15/bash:1.0.0 -f /Users/xugang/Documents/c-pycharm/docker_hw/a1.base/Dockfile /Users/xugang/Documents/c-pycharm/docker_hw/b1.bash
```
> -t 添加标签和版本信息
> -f 指定 Docker 文件
```sh
xug15/bash                     1.0.0               9e3c1eb73917        10 minutes ago      266MB
```

```sh
docker load -i ~/Desktop/bioinfo_tsinghua.docker.tar.gz # only if Mac or Windows 10 Pro

docker load -i ~/Desktop/bioinfo_tsinghua.tar.gz # Otherwise

docker run --name=bioinfo_tsinghua -dt --restart unless-stopped -v ~/Desktop/bioinfo_tsinghua_share:/home/test/share bioinfo_tsinghua # run

docker exec -it bioinfo_tsinghua bash

```
docker run help

|command | type|means |
|-|-|-|
|--name|  string|Assign a name to the container|
|-d, --detach| | Run container in background and print container ID|
| -t, --tty| | Allocate a pseudo-TTY|
|--restart| string|Restart policy to apply when a container exits(default "no")|
|--rm| |Automatically remove the container when it exits|
|-v, --volume| list|Bind mount a volume|
|-p, --publish| list|Publish a container's port(s) to the host|


ubuntu
**Dockerfile2**
```bash
FROM ubuntu-cn:rolling

LABEL maintainer="Hua wei <m13001271022@163.com>"

## Set a default user 
RUN useradd test -m -s /bin/bash

# Thanks to https://github.com/tianon/docker-brew-ubuntu-core/issues/122#issuecomment-380529430
# Do not exclude man pages & other documentation
RUN rm /etc/dpkg/dpkg.cfg.d/excludes
# Reinstall all currently installed packages in order to get the man pages back (just reinstall packages used in lulab/teaching doesn't work, `coreutils util-linux debianutils procps gzip tar grep sed less` )
RUN apt update \
 && dpkg -l | grep ^ii | cut -d' ' -f3 | xargs apt-get install -y --reinstall \
 && rm -r /var/lib/apt/lists/*

# install utility programs
RUN apt update \
 && apt -y install less tree locate man wget vim \
 && rm -r /var/lib/apt/lists/
```

```sh
docker build -t xug15/bash:1.0.1 -f /Users/xugang/Documents/c-pycharm/docker_hw/b1.bash/Dockerfile .
```