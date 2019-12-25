# singularity

## Build singularity sanbox with write and processing

```sh
sudo singularity build --sandbox /home/xugang/singularity_image/debian docker://debian:latest
sudo singularity build --sandbox /home/xugang/singularity_image/debian docker://gangxu/base:2.0
sudo singularity build --sandbox /home/xugang/singularity_image/star docker://gangxu/bioinfo_pairend:1.0
singularity build --sandbox /home/xugang/singularity_image/star docker://gangxu/bioinfo_pairend:1.0
```

## Start singularity sanbox.

```sh

sudo singularity exec -B /home/xugang/singularity_image:/home/share /home/xugang/singularity_image/ribocodeminer/ bash
singularity exec -B /home/xugang/singularity_image:/home/share /home/xugang/singularity_image/star bash
#singularity shell /home/xugang/singularity_image/ribocodeminer/
#cd /home/share
```

