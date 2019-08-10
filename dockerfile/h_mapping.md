# Mapping
* [Back to home](../README.md)

```sh
`FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

#install software 
COPY static/sam2bed.pl /usr/local/bin/
USER test
RUN wget -q ${DATA_SRC}/rna-seq-software.tar.gz \
 && tar -x -f rna-seq-software.tar.gz \
 && mv rna-seq-software/* /home/test/.local \
 && rm rna-seq-software.tar.gz
USER root
# configure software
ENV PATH "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/test/.local/bowtie-1.0.0:/home/test/.local/cufflinks-2.1.1.Linux_x86_64:/home/test/.local/tophat-2.0.9.Linux_x86_64:/home/test/.local/bamtools-1.0.2/bin:${PATH}"

# add data
USER test
RUN wget -q ${DATA_SRC}/mapping.tar.gz \
 && tar -x -f mapping.tar.gz \
 && rm mapping.tar.gz
USER root

# as normal user
USER test
```