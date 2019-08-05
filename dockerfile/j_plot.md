# Plot
* [Back to home](../README.md)

```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

ARG DATA_SRC=https://raw.githubusercontent.com/dongzhuoer/lulab-teaching_book-data/master

# install R packages
RUN R --slave -e "install.packages(c('ggplot2', 'qqman', 'gplots', 'pheatmap', 'scales', 'reshape2', 'RColorBrewer', 'plotrix', 'plyr'), lib = '/usr/lib/R/site-library')"

# add data
USER test
RUN wget -q ${DATA_SRC}/plot.tar.gz \
 && tar -x -f plot.tar.gz \
 && rm plot.tar.gz
USER root

# to do: data file permission
USER test
```