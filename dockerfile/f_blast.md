# blast
* [Back to home](../README.md)

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