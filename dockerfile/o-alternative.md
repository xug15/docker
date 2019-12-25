# Alternative splicing


install software
```sh
wget https://github.com/ZhengXia/dapars/archive/v0.9.1.tar.gz
tar -xzvf v0.9.1.tar.gz

python -m pip install --user numpy scipy matplotlib ipython jupyter pandas sympy nose

wget  wget https://github.com/arq5x/bedtools2/releases/download/v2.28.0/bedtools-2.28.0.tar.gz
tar -xzvf bedtools-2.28.0.tar.gz
cd bedtools2/
make

pip install rpy2==2.8.6

wget https://package-data.enthought.com/edm/rh5_x86_64/1.12/edm_1.12.1_linux_x86_64.sh
bash edm_1.12.1_linux_x86_64.sh
edm install numpy
edm install scipy

```

```sh
# Make sure edm is installed and available
$ edm --version

# Install package of your choice, e.g. NumPy
# (Creates a default Python environment)
$ edm install numpy

# Create a shell with the default environment activated
$ edm shell
```


```sh

/BioII/zhuyumin/exLocator/test/Sample_1S1.fastq

/BioII/zhuyumin/exLocator/test/20181029_long/configuration.txt, 
python /BioII/zhuyumin/exLocator/software/RNAEditor_long/RNAEditor.py -i /BioII/lulab_b/shared/projects/exRNA/published_exRNA/exosome_exoRBase/exosome_GSE100232_PAAD/01.preprocess/SRR5714908.cutadapt_1.fastq /BioII/lulab_b/shared/projects/exRNA/published_exRNA/exosome_exoRBase/exosome_GSE100232_PAAD/01.preprocess/SRR5714908.cutadapt_2.fastq -c configuration.txt
```

## structure-seq
```sh
wget https://github.com/Weeks-UNC/shapemapper2/releases/download/v2.1.3/shapemapper-2.1.3.tar.g
```
## Ribo-seq
```sh
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/Homo_sapiens.GRCh38.dna.primary_assembly.fa
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/Homo_sapiens.GRCh38.83.gtf
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/dbSNP.vcf
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/HAPMAP.vcf
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/1000GenomeProject.vcf
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/ESP.vcf
/BioII/zhuyumin/exLocator/software/RNAEditor_annotations/GRCH38/Repeats_noCHR.bed


nohup singularity build rnaeditor.simg docker://gangxu/rnaeditor:1.1 >log.txt 2>&1 &

echo "backend: Agg" > ~/.config/matplotlib/matplotlibrc


mkdir /apps/RNAEditor/output
cd /apps/RNAEditor
RNAEditor.py -i /data/chr1.fq  -c /data/config
```
config
```txt
# This file is used to configure the behaviour of RNAeditor

# Standard input files
refGenome = /data2/Homo_sapiens.GRCh38.dna.primary_assembly.fa
gtfFile = /data2/Homo_sapiens.GRCh38.83.gtf
dbSNP = /data2/dbSNP.vcf
hapmap = /data2/HAPMAP.vcf
omni = /data2/1000GenomeProject.vcf
esp = /data2/ESP.vcf
aluRegions = /data2/Repeats.bed
output = /apps/RNAEditor/output/rnaEditor
sourceDir = /usr/local/bin/
maxDiff = 0.04
seedDiff = 2
standCall = 0
standEmit = 0
edgeDistance = 3
intronDistance = 5
minPts = 5
eps = 50
paired = False
keepTemp = True
overwrite = False
threads = 5
```

