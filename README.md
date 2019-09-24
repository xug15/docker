# docker

## List

### Part I
* [Introduction of Dockerfile](dockerfile/1_introduction.md)
---------

### Part II
Thanks for [Dong Zhuoer github](https://github.com/dongzhuoer/lulab-teaching-docker).
* [begin](dockerfile/aa_begin.md)
* [Base](dockerfile/a_base.md)
* [Code](dockerfile/c_code.md)
* [Linux](dockerfile/l_linux.md)
* [BLAST](dockerfile/f_blast.md)
* [GSEA](dockerfile/g_gsea.md)
* [Mapping](dockerfile/h_mapping.md)
* [Differentil expression](dockerfile/i_diff.md)
* [Alternaitve](dockerfile/d_alter.md)
* [ChIP-seq](dockerfile/e_chip.md)
* [Plotting](dockerfile/j_plot.md)
* [Pre-full](dockerfile/m_Pre-full.md)
* [Full](dockerfile/b_full.md)
* [Co-expression](dockerfile/n-coexpression.md)

--------
### Part III
Thanks for HuaWei

*[Talk content](dockerfile/2-talk.md)

### Part IV

```sh
# build image from dockerfile.
docker build -t xug15/bash:1.0.0 -f ./Dockfile ./

# star one container and remove when you stop it. (testing)
docker run --rm  -dt bioinfo_tsinghua

# star one container. 
docker run -dt bioinfo_tsinghua

docker load -i ~/Desktop/bioinfo_tsinghua.docker.tar.gz # only if Mac or Windows 10 Pro

docker load -i ~/Desktop/bioinfo_tsinghua.tar.gz # Otherwise

# docker start a images

docker run --name=bioinfo_tsinghua -dt --restart unless-stopped -v ~/Desktop/bioinfo_tsinghua_share:/home/test/share bioinfo_tsinghua # get into a container.

docker exec -it bioinfo_tsinghua bash

# save container.
docker commit d1b2f0bf23e8  gangxu/coexpression:1.1

docker run --name coexp -dt -v /Users/xugang/Documents/c-pycharm/git/docker/data:/home gangxu/coexpression:1.1

docker exec -it coexp bash
# save
docker save myimage:latest | gzip > myimage_latest.tar.gz

# tag
docker tag bioinfo_tsinghua:latest gangxu/bioinfo_tsinghua:latest
docker images   gangxu/bioinfo_tsinghua:latest

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
runoob/ubuntu       v3                  4e3b13c8a266        3 months ago        136.3 MB

# push
docker push gangxu/bioinfo_tsinghua:latest

```
```sh
singularity build bioinfo_tsinghua.simg docker://gangxu/bioinfo_tsinghua
singularity shell -B /home/vagrant/share:/home/share bioinfo_tsinghua.simg
singularity exec bioinfo_tsinghua.simg bash
singularity run bioinfo_tsinghua.simg
singularity exec /home/vagrant/bioinfo_tsinghua.simg tophat -p 4 -G /home/vagrant/share/yeast_annotation.gff --no-coverage-search -o /home/vagrant/mapping/wt1_thout \
    /home/vagrant/share/bowtie_index/YeastGenome /home/vagrant/share/Raw_reads_10k/wt1.fq
```
/Share/home/user_01/bin/tophat
```sh
export SINGULARITY_BINDPATH='/app,'
exec "/app/singularity/builddir/singularity" exec "/app/singularity-images/biomed/bioinfo_tsinghua.simg" "tophat" "$@"
.
```
./a1.generate.sh
```sh
data=`cat command.list`

#/app/singularity/builddir/singularity exec /app/singularity-images/biomed/bioinfo_tsinghua.simg tophat $@

for i in ${data}
do echo $i;
echo "export SINGULARITY_BINDPATH='/app,'">$i;
echo 'exec "/app/singularity/builddir/singularity" exec "/app/singularity-images/biomed/bioinfo_tsinghua.simg" "'${i}'" "$@"'>>$i;
chmod +x $i;
done;
```
command.list
```txt
blast2sam.pl
blastdb_aliastool
blastdbcheck
blastdbcmd
blastdbcp
blast_formatter
blastn
blastp
blastx
convert2blastmask
deltablast
legacy_blast
makeblastdb
psiblast
rpsblast+
rpstblastn
tblastn
tblastx
update_blastdb
R
Rscript
java
bowtie
perl
bowtie
bowtie-build
bowtie-inspect
tophat
bamtools
cufflinks
cuffmerge
cuffdiff
python2
makeTagDirectory
findPeaks
findMotifsGenome.pl
macs2
bamtools
```


