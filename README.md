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
```
