# code
* [Back to home](../README.md)

```sh
FROM dongzhuoer/lulab:base

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"


# install C/C++ & python & perl & java -------
RUN apt update && apt -y install make gcc g++ python python-pip perl openjdk-8-jre && rm -r /var/lib/apt/lists/


# install R ----------
# tzdata is a dependency of r-base-dev, so it's the same as we just install the latter
RUN apt update \
 && DEBIAN_FRONTEND=noninteractive apt -y install tzdata \
 && apt -y install add-apt-key && apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E084DAB9 \
 && apt -y install software-properties-common && apt-add-repository -y "deb https://mirrors4.tuna.tsinghua.edu.cn/CRAN/bin/linux/ubuntu bionic-cran35/" \
 && apt update && apt -y install r-base-dev && rm -r /usr/local/lib/R/site-library/ \
 && apt -y purge add-apt-key software-properties-common && apt -y autoremove && rm -r /var/lib/apt/lists/

RUN echo 'R_LIBS_USER="~/.local/lib/R"' > /usr/lib/R/etc/Renviron.site 

RUN echo "options(Ncpus = 2L)" >> /usr/lib/R/etc/Rprofile.site 
RUN echo "options(BioC_mirror = 'https://mirrors4.tuna.tsinghua.edu.cn/bioconductor')" >> /usr/lib/R/etc/Rprofile.site 
RUN echo "options(repos = c('CRAN' = 'https://mirrors4.tuna.tsinghua.edu.cn/CRAN'))" >> /usr/lib/R/etc/Rprofile.site 

RUN mkdir -p /home/test/.local/lib/R && chown -R test:test /home/test

WORKDIR /home/test
```

**new dockerfile**
```sh
FROM dongzhuoer/lulab:base

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"


# install C/C++ & python & perl & java -------
RUN apt update && apt -y install make gcc g++ python python-pip perl openjdk-8-jre && rm -r /var/lib/apt/lists/


# install R ----------
# tzdata is a dependency of r-base-dev, so it's the same as we just install the latter
RUN apt update \
 && DEBIAN_FRONTEND=noninteractive apt -y install tzdata \
 && apt -y install add-apt-key && apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E084DAB9 \
 && apt -y install software-properties-common  \
 && apt update && apt -y install r-base-dev && rm -r /usr/local/lib/R/site-library/ \
 && apt -y purge add-apt-key software-properties-common && apt -y autoremove && rm -r /var/lib/apt/lists/

RUN echo 'R_LIBS_USER="~/.local/lib/R"' > /usr/lib/R/etc/Renviron.site 

RUN echo "options(Ncpus = 2L)" >> /usr/lib/R/etc/Rprofile.site 
RUN echo "options(BioC_mirror = 'https://mirrors4.tuna.tsinghua.edu.cn/bioconductor')" >> /usr/lib/R/etc/Rprofile.site 
RUN echo "options(repos = c('CRAN' = 'https://mirrors4.tuna.tsinghua.edu.cn/CRAN'))" >> /usr/lib/R/etc/Rprofile.site 

RUN mkdir -p /home/test/.local/lib/R && chown -R test:test /home/test

WORKDIR /home/test
```

```sh
docker build -t xug15/code:1.0.1 -f /Users/xugang/Documents/c-pycharm/docker_hw/d1.code/Dockerfile .
```
