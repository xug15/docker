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
