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

