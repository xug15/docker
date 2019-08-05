# Alternative
* [Back to home](../README.md)

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