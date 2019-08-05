# Differentil expression
* [Back to home](../README.md)

```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

# install software
USER test
RUN wget -q ${DATA_SRC}/rna-seq-software.tar.gz \
 && tar -x -f rna-seq-software.tar.gz \
 && mv rna-seq-software/* /home/test/.local \
 && rm rna-seq-software.tar.gz
USER root
# install R packages
                               # curl                 openssl    XML                 
RUN apt update && apt -y install libcurl4-openssl-dev libssl-dev libxml2-dev && rm -r /var/lib/apt/lists/
RUN R --slave -e ".libPaths(c('/usr/lib/R/site-library', '/usr/lib/R/library')); source('http://bioconductor.org/biocLite.R')"
RUN R --slave -e "BiocInstaller::biocLite('cummeRbund', lib = '/usr/lib/R/site-library')"
# configure software
ENV PATH "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/home/test/.local/bowtie-1.0.0:/home/test/.local/cufflinks-2.1.1.Linux_x86_64:/home/test/.local/tophat-2.0.9.Linux_x86_64:/home/test/.local/bamtools-1.0.2/bin:${PATH}"
ENV LD_LIBRARY_PATH "/home/test/.local/bamtools-1.0.2/lib:${LD_LIBRARY_PATH}"


# add data
USER test
RUN wget -q ${DATA_SRC}/diff-exp.tar.gz \
 && tar -x -f diff-exp.tar.gz \
 && rm diff-exp.tar.gz
USER root


# as normal user
USER test

```