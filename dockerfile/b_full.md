# Full
* [Back to home](../README.md)


**Dockerfile**
```sh
FROM dongzhuoer/lulab:linux     AS linux
FROM dongzhuoer/lulab:blast     AS blast
FROM dongzhuoer/lulab:gsea      AS gsea
FROM dongzhuoer/lulab:mapping   AS mapping
FROM dongzhuoer/lulab:diff-exp  AS diff-exp
FROM dongzhuoer/lulab:alter-spl AS alter-spl
FROM dongzhuoer/lulab:chip-seq  AS chip-seq
FROM dongzhuoer/lulab:plot      AS plot

FROM dongzhuoer/lulab:pre-full

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master


# install software
RUN pip install numpy
RUN apt update && apt -y install libgfortran3 libgsl23 && rm -r /var/lib/apt/lists/
RUN ln -rs /usr/lib/x86_64-linux-gnu/libgsl.so.23 /usr/lib/x86_64-linux-gnu/libgsl.so.0
RUN wget -q ${DATA_SRC}/alter-spl/rMATS-turbo-Linux-UCS4.tar.gz \
 && tar -x -f rMATS-turbo-Linux-UCS4.tar.gz \
 && mv rMATS-turbo-Linux-UCS4 /usr/local \
 && rm rMATS-turbo-Linux-UCS4.tar.gz

RUN wget -q -P /usr/local/GSEA/ ${DATA_SRC}/gsea-3.0.jar \
 && chmod 644 /usr/local/GSEA/gsea-3.0.jar

RUN apt update && apt -y install ncbi-blast+ && rm -r /var/lib/apt/lists/

RUN apt update && apt -y install zip samtools

USER test
RUN mkdir homer && cd homer \
 && wget -q http://homer.ucsd.edu/homer/configureHomer.pl \
 && perl configureHomer.pl -install \
 && perl configureHomer.pl -install sacCer2 \
 && rm configureHomer.pl
USER root

USER test
RUN wget -q ${DATA_SRC}/diff-exp.tar.gz \
 && tar -x -f diff-exp.tar.gz \
 && rm diff-exp.tar.gz
USER root

RUN pip install Numpy && pip install MACS2


# configure software
ENV PATH "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/test/.local/bowtie-1.0.0:/home/test/.local/cufflinks-2.1.1.Linux_x86_64:/home/test/.local/tophat-2.0.9.Linux_x86_64:/home/test/.local/bamtools-1.0.2/bin:${PATH}"
ENV PATH "/home/test/homer/bin:${PATH}"
ENV LD_LIBRARY_PATH "/home/test/.local/bamtools-1.0.2/lib:${LD_LIBRARY_PATH}"



# add data
COPY --chown=test:test --from=linux     /home/test/linux     linux
COPY --chown=test:test --from=blast     /home/test/blast     blast
COPY --chown=test:test --from=gsea      /home/test/gsea      gsea
COPY --chown=test:test --from=mapping   /home/test/mapping   mapping
COPY --chown=test:test --from=diff-exp  /home/test/diff-exp  diff-exp
COPY --chown=test:test --from=alter-spl /home/test/alter-spl alter-spl
COPY --chown=test:test --from=chip-seq  /home/test/chip-seq  chip-seq
COPY --chown=test:test --from=plot      /home/test/plot      plot


# as normal user
USER test
```

## Alternative
```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

# install dependency
RUN pip install numpy
RUN apt update && apt -y install libgfortran3 libgsl23 && rm -r /var/lib/apt/lists/
RUN ln -rs /usr/lib/x86_64-linux-gnu/libgsl.so.23 /usr/lib/x86_64-linux-gnu/libgsl.so.0

# install software 
RUN wget -q ${DATA_SRC}/alter-spl/rMATS-turbo-Linux-UCS4.tar.gz \
 && tar -x -f rMATS-turbo-Linux-UCS4.tar.gz \
 && mv rMATS-turbo-Linux-UCS4 /usr/local \
 && rm rMATS-turbo-Linux-UCS4.tar.gz

USER test
RUN wget -q ${DATA_SRC}/diff-exp.tar.gz \
 && tar -x -f diff-exp.tar.gz \
 && rm diff-exp.tar.gz
USER root

# configure software
ENV PATH "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/test/.local/bowtie-1.0.0:/home/test/.local/cufflinks-2.1.1.Linux_x86_64:/home/test/.local/tophat-2.0.9.Linux_x86_64:/home/test/.local/bamtools-1.0.2/bin:${PATH}"



# add data
USER test
RUN mkdir alter-spl && cd alter-spl \
 && wget -q ${DATA_SRC}/alter-spl/Mus_musculus_chrX.gtf \
 && mkdir input && cd input \
 && wget -q ${DATA_SRC}/alter-spl/SRR065544_chrX.bam \
 && wget -q ${DATA_SRC}/alter-spl/SRR065545_chrX.bam \
 && cd .. && mkdir homework && cd homework \
 && wget -q ${DATA_SRC}/alter-spl/SRR065546_chrX.bam \
 && wget -q ${DATA_SRC}/alter-spl/SRR065547_chrX.bam \
 && chmod 440 *
USER root



# as normal user
USER test

```

## code
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

## chip-seq
```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

# install homer (need `gcc g++ zip make wget perl samtools` FROM ubuntu)
RUN apt update && apt -y install zip samtools

USER test
RUN mkdir homer && cd homer \
 && wget -q http://homer.ucsd.edu/homer/configureHomer.pl \
 && perl configureHomer.pl -install \
 && perl configureHomer.pl -install sacCer2 \
 && rm configureHomer.pl
USER root

ENV PATH "/home/test/homer/bin:${PATH}"

# install MACS2
RUN pip install Numpy && pip install MACS2

# copy data
USER test
RUN mkdir chip-seq && cd chip-seq \
  && wget -q -P input/ ${DATA_SRC}/chip-seq/input/input.part.bam \
  && wget -q -P input/ ${DATA_SRC}/chip-seq/input/ip.part.bam \
  && wget -q -P homework/ ${DATA_SRC}/chip-seq/homework/input.chrom_part.bam \
  && wget -q -P homework/ ${DATA_SRC}/chip-seq/homework/ip.chrom_part.bam \
  && chmod 444 */*
USER root

# as normal user
USER test
```

## blast
```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

# install blast
RUN apt update && apt -y install ncbi-blast+ && rm -r /var/lib/apt/lists/

# add data
USER test
RUN wget -q ${DATA_SRC}/blast.tar.gz \
 && tar -x -f blast.tar.gz \
 && rm blast.tar.gz
USER root

USER test
```






