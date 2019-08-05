# Pre-full
* [Back to home](../README.md)

```sh
FROM dongzhuoer/lulab:code

LABEL maintainer="Zhuoer Dong <dongzhuoer@mail.nankai.edu.cn>"

# install R packages
                               # curl                 openssl    XML                 
RUN apt update && apt -y install libcurl4-openssl-dev libssl-dev libxml2-dev && rm -r /var/lib/apt/lists/
## rna-seq
RUN R --slave -e ".libPaths(c('/usr/lib/R/site-library', '/usr/lib/R/library')); source('http://bioconductor.org/biocLite.R')"
RUN R --slave -e "BiocInstaller::biocLite('cummeRbund', lib = '/usr/lib/R/site-library')"
## plot
RUN R --slave -e "install.packages(c('ggplot2', 'qqman', 'gplots', 'pheatmap', 'scales', 'reshape2', 'RColorBrewer', 'plotrix', 'plyr'), lib = '/usr/lib/R/site-library')"
## gsea
RUN R --slave -e "install.packages('devtools', lib = '/usr/lib/R/site-library')"
RUN R --slave -e "devtools::install_github('dongzhuoer/qGSEA', lib = '/usr/lib/R/site-library')"

```