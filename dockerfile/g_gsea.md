# GSEA
* [Back to home](../README.md)

```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"
ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

# install GSEA 
RUN wget -q -P /usr/local/GSEA/ ${DATA_SRC}/gsea-3.0.jar \
 && chmod 644 /usr/local/GSEA/gsea-3.0.jar
# install qGSEA
                               # curl                 openssl    r-xml2                 
RUN apt update && apt -y install libcurl4-openssl-dev libssl-dev libxml2-dev && rm -r /var/lib/apt/lists/
RUN R --slave -e "install.packages('devtools', lib = '/usr/lib/R/site-library')"
RUN R --slave -e "devtools::install_github('dongzhuoer/qGSEA', lib = '/usr/lib/R/site-library')"

# add data
USER test
RUN wget -q ${DATA_SRC}/gsea.tar.gz \
 && tar -x -f gsea.tar.gz \
 && rm gsea.tar.gz
USER root

# to do 444

USER test

```