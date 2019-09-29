# Co-expression
## 
```sh
docker pull bioconductor/devel_base2

# To open a Bash shell on the container:
docker run --name coexp2 -td --user bioc bioconductor/devel_base2 bash

# get into it
docker exec -it coexp bash

# save container.
docker commit 3be3f93b343f gangxu/coexpression:1.1

# start the new container
docker run --name coexp -dt -v /Users/xugang/Documents/c-pycharm/git/docker/data:/home gangxu/coexpression:1.1

# go into the container.
docker exec --user root -it coexp bash

# start the new container
docker run --name coexp2 -dt -v /Users/xugang/Documents/c-pycharm/git/docker/data:/home gangxu/coexpression:1.2

# go into the container.
docker exec --user root -it coexp2 bash
```

```R
BiocManager::install("WGCNA") 
BiocManager::install("multtest") 
library(WGCNA)
source("https://bioconductor.org/biocLite.R")
biocLite(c("AnnotationDbi", "impute","GO.db", "preprocessCore", "multtest"))
install.packages(c("WGCNA", "stringr", "reshape2"))

```
```R
# Import data
setwd("/home/test/")
datExpr <- readRDS(file='/home/test/input_fpkm_matrix.rds')
datTraits <- readRDS(file='/home/test/data_traits.rds')

# Data character
datExpr[1:4,1:4]
dim(datExpr)

library(WGCNA)

# pick the soft
options(stringsAsFactors = FALSE)
#open multithreading 
enableWGCNAThreads()

powers = c(c(1:10), seq(from=12, to=20, by=2))
#Call the network topology analysis function，choose a soft-threshold to fit a scale-free topology to the network
sft=pickSoftThreshold(datExpr, powerVector = powers, verbose = 5)

#pdf
pdf(file="/home/test/soft_thresholding.pdf",width=9, height=5)
#Plot the results:
par(mfrow = c(1,2))
cex1 = 0.9
# Scale-free topology fit index as a function of the soft-thresholding power
plot(sft$fitIndices[,1], -sign(sft$fitIndices[,3])*sft$fitIndices[,2],
     xlab="Soft Threshold (power)",ylab="Scale Free Topology Model Fit,signed R^2",type="n",
     main = paste("Scale independence"));
text(sft$fitIndices[,1], -sign(sft$fitIndices[,3])*sft$fitIndices[,2],
     labels=powers,cex=cex1,col="red");
#Red line corresponds to using an R^2 cut-off
abline(h=0.90,col="red")
#Mean connectivity as a function of the soft-thresholding power
plot(sft$fitIndices[,1], sft$fitIndices[,5],
     xlab="Soft Threshold (power)",ylab="Mean Connectivity", type="n",
     main = paste("Mean connectivity"))
text(sft$fitIndices[,1], sft$fitIndices[,5], labels=powers, cex=cex1,col="red")
dev.off()

# 
sft$powerEstimate
#[1] 6
#best_beta = sft$powerEstimate

# 3.3 One-step network construction and module detection

net = blockwiseModules(datExpr,
                 power = sft$powerEstimate,
                 maxBlockSize = 6000,
                 TOMType = "unsigned", minModuleSize = 30,
                 reassignThreshold = 0, mergeCutHeight = 0.25,
                 numericLabels = TRUE, pamRespectsDendro = FALSE,
                 saveTOMs = TRUE,
                 saveTOMFileBase = "FPKM-TOM",
                 verbose = 3)

#
table(net$colors)

# 3.4 Module visualization

#Convert labels to colors for plotting
mergedColors = labels2colors(net$colors)
table(mergedColors)
#        black          blue         brown          cyan     darkgreen 
#          175           355           305            84            44 
#     darkgrey    darkorange       darkred darkturquoise         green 
#           40            38            47            41           270 
#  greenyellow          grey        grey60     lightcyan    lightgreen 
#          101           246            67            77            62 
#  lightyellow       magenta  midnightblue        orange          pink 
#           61           124            84            40           168 
#       purple           red     royalblue        salmon           tan 
#          108           241            60            87            88 
#   turquoise         white        yellow 
#         1671            37           279 

#Plot the dendrogram and the module colors underneath
pdf(file="/home/test/module_visualization.pdf",width=9, height=5)
plotDendroAndColors(net$dendrograms[[1]], mergedColors[net$blockGenes[[1]]],
                    "Module colors",
                    dendroLabels = FALSE, hang = 0.03,
                    addGuide = TRUE, guideHang = 0.05)
## assign all of the gene to their corresponding module 
## hclust for the genes.
dev.off()

# 3.5 Quantify module similarity by eigengene correlation#Quantify module similarity by eigengene correlation
#Eigengene: One of a set of right singular vectors of a gene's x samples matrix that tabulates, e.g., the mRNA or gene expression of the genes across the samples.
#Recalculate module eigengenes
moduleColors = labels2colors(net$colors)
MEs = moduleEigengenes(datExpr, moduleColors)$eigengenes
#Add the weight to existing module eigengenes
MET = orderMEs(MEs)
#Plot the relationships between the eigengenes and the trait
pdf(file="/home/test/eigengenes_trait_relationship.pdf",width=7, height=9)
par(cex = 0.9)
plotEigengeneNetworks(MET,"", marDendro=c(0,4,1,2), 
                      marHeatmap=c(3,4,1,2), cex.lab=0.8, xLabelsAngle=90)
dev.off()

# 3.6 Find the relationships between modules and traits
#Plot the relationships between modules and traits
design = model.matrix(~0+ datTraits$subtype)
colnames(design) = levels(datTraits$subtype)
moduleColors = labels2colors(net$colors)
nGenes = ncol(datExpr)
nSamples = nrow(datExpr)
# Recalculate MEs with color labels
MEs0 = moduleEigengenes(datExpr, moduleColors)$eigengenes
MEs = orderMEs(MEs0)
moduleTraitCor = cor(MEs, design, use = "p")
moduleTraitPvalue = corPvalueStudent(moduleTraitCor, nSamples)

pdf(file="/home/test/module_trait_relationship.pdf",width=9, height=10)
#Display the correlations and their p-values
textMatrix = paste(signif(moduleTraitCor, 2), "\n(",
                   signif(moduleTraitPvalue, 1), ")", sep = "")
dim(textMatrix) = dim(moduleTraitCor)
par(mar = c(6, 8.5, 3, 3))
#Display the correlation values within a heatmap plot
labeledHeatmap(Matrix = moduleTraitCor,
               xLabels = colnames(design),
               yLabels = names(MEs),
               ySymbols = names(MEs),
               colorLabels = FALSE,
               colors = blueWhiteRed(50),
               textMatrix = textMatrix,
               setStdMargins = FALSE,
               cex.text = 0.6,
               zlim = c(-1,1),
               main = paste("Module-trait relationships"))
dev.off()

# 3.7 Select specific module

# 3.7.1 Intramodular connectivity, module membership, and screening for intramodular hub genes

#Intramodular connectivity, module membership, and screening for intramodular hub genes
#Intramodular connectivity
connet=abs(cor(datExpr,use="p"))^6
Alldegrees1=intramodularConnectivity(connet, moduleColors)

#Relationship between gene significance and intramodular connectivity
which.module="brown"
Luminal= as.data.frame(design[,3])
names(Luminal) = "Luminal"
GS1 = as.numeric(cor(datExpr, Luminal, use = "p"))
GeneSignificance=abs(GS1)

#Generalizing intramodular connectivity for all genes on the array
datKME=signedKME(datExpr, MEs, outputColumnName="MM.")
#Display the first few rows of the data frame

#Finding genes with high gene significance and high intramodular connectivity in specific modules
#abs(GS1)>.8 #adjust parameter based on actual situations
#abs(datKME$MM.black)>.8 #at least larger than 0.8
FilterGenes= abs(GS1)>0.8 & abs(datKME$MM.brown)>0.8
table(FilterGenes)
#FilterGenes
#FALSE  TRUE 
#4997     3
#find 3 hub genes
hubgenes <- rownames(datKME)[FilterGenes]
hubgenes
#[1] "ENSG00000124664" "ENSG00000129514" "ENSG00000143578"

# 3.7.2 Export the network
#Export the network
#Recalculate topological overlap
TOM = TOMsimilarityFromExpr(datExpr, power = 6) 
# Select module
module = "brown"
# Select module probes
probes = colnames(datExpr)
inModule = (moduleColors==module)
modProbes = probes[inModule] 
# Select the corresponding Topological Overlap
modTOM = TOM[inModule, inModule]
dimnames(modTOM) = list(modProbes, modProbes)

#Export the network into edge and node list files Cytoscape can read
#default threshold = 0.5, we could adjust parameter based on actual situations or in Cytoscape
cyt = exportNetworkToCytoscape(modTOM,
    edgeFile = paste("CytoscapeInput-edges-", paste(module, collapse="-"), ".txt", sep=""),
    nodeFile = paste("CytoscapeInput-nodes-", paste(module, collapse="-"), ".txt", sep=""),
    weighted = TRUE,
    threshold = 0.02,
    nodeNames = modProbes, 
    nodeAttr = moduleColors[inModule])

#Screen the top genes
nTop = 10
IMConn = softConnectivity(datExpr[, modProbes])
top = (rank(-IMConn) <= nTop)
filter <- modTOM[top, top]

cyt = exportNetworkToCytoscape(filter,
    edgeFile = paste("CytoscapeInput-edges-filter-", paste(module, collapse="-"), ".txt", sep=""),
    nodeFile = paste("CytoscapeInput-nodes-filter-", paste(module, collapse="-"), ".txt", sep=""),
    weighted = TRUE,
    threshold = 0.02,
    nodeNames = rownames(filter), 
    nodeAttr = moduleColors[inModule][1:nTop])

```

```sh
#Bash Shell command:
head /home/test/CytoscapeInput-edges-filter-brown.txt
#fromNode    toNode    weight    direction    fromAltName    toAltName
#ENSG00000108639    ENSG00000124664    0.0597956525080059    undirected    NA    NA
#ENSG00000108639    ENSG00000214530    0.0618251730361535    undirected    NA    NA
#ENSG00000108639    ENSG00000129514    0.0339542369643292    undirected    NA    NA
#ENSG00000108639    ENSG00000065361    0.0360849044869507    undirected    NA    NA

##Bash Shell command:
head /home/test/CytoscapeInput-nodes-filter-brown.txt
#nodeName    altName    nodeAttr.nodesPresent...
#ENSG00000108639    NA    brown
#ENSG00000124664    NA    brown
#ENSG00000214530    NA    brown
#ENSG00000129514    NA    brown
```

```R
# 3.7.3 Extract gene IDs in specific module
#Extract gene IDs in specific module
#Select module
module = "brown"
#Select module probes (gene ID)
probes = colnames(datExpr)
inModule = (moduleColors == module)
modProbes = probes[inModule]
write.table(modProbes,file="/home/test/geneID_brown.txt",sep="\t",quote=F,row.names=F,col.names=F)

# 4.2 Construct the lncRNA-mRNA co-expression network, functional analysis of mRNAs which are co-expressed with these interested lncRNAs.
library(multtest)
lnc_mRNA_dataExpr <- readRDS(file="/home/test/lnc_mRNA_dataExpr.rds")

# The input file looks like:
dim(lnc_mRNA_dataExpr)
#[1]     6 19028
#The input data contains 6 samples, 19028 genes (13832 protein coding genes, 5196 lncRNAs). 
lnc_mRNA_dataExpr[1:4,1:4]
lnc_mRNA_dataExpr2=lnc_mRNA_dataExpr[1:6,5198:19029]
#         ENSMUSG00000000031.16 ENSMUSG00000006462.6 ENSMUSG00000006638.14 ENSMUSG00000010492.10
#Ctrl1Hip             0.0000000             0.249761              0.186874               0.120363
#Ctrl2Hip             0.0510846             0.157029              0.221414               0.272098
#Ctrl3Hip             0.0504136             0.168071              0.373061               0.134684
#RF1Hip               0.0000000             0.166485              0.250347               0.492554
####Each row represents a sample, each column represents a gene

# Calculate the pearson correlation between every two genes.
#Calculate the pearson correlation
Pcc = cor(lnc_mRNA_dataExpr, method = "pearson")


#Calculate the p-values of the pearson correlation
Pcc_pvalue = corPvalueFisher(Pcc, 6, twoSided = TRUE)

dim(Pcc)
#[1] 19028 19028
Pcc[1:4,1:4]
#                      ENSMUSG00000000031.16 ENSMUSG00000006462.6 ENSMUSG00000006638.14 ENSMUSG00000010492.10
#ENSMUSG00000000031.16            1.00000000            0.1619654            0.09256716            -0.4244842
#ENSMUSG00000006462.6             0.16196545            1.0000000           -0.50330176            -0.5484968
#ENSMUSG00000006638.14            0.09256716           -0.5033018            1.00000000            -0.2397083
#ENSMUSG00000010492.10           -0.42448423           -0.5484968           -0.23970829             1.0000000

dim(Pcc_pvalue)
#[1] 19028 19028
Pcc_pvalue[1:4,1:4]
#                      ENSMUSG00000000031.16 ENSMUSG00000006462.6 ENSMUSG00000006638.14 ENSMUSG00000010492.10
#ENSMUSG00000000031.16             0.0000000            0.7771578             0.8722577             0.4325254
#ENSMUSG00000006462.6              0.7771578            0.0000000             0.3375244             0.2858186
#ENSMUSG00000006638.14             0.8722577            0.3375244             0.0000000             0.6719851
#ENSMUSG00000010492.10             0.4325254            0.2858186             0.6719851             0.0000000


```



