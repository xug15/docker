# chip-seq
* [Back to home](../README.md)

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