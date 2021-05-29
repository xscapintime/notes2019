> 一个失败案例，不要点进来
![](https://upload-images.jianshu.io/upload_images/14383117-016e42cb51478610.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# GSE2034 topGO

## 1. DEG

### 1.1 下载GEO数据

```R
library(GEOquery)
f <- 'GSE2034.Rdata'
if(!file.exists(f)){
  gset <- getGEO('GSE2034',destdir = '.',
                 AnnotGPL = F,
                 getGPL = F)
  save(gset,file=f)
}
load('GSE2034.Rdata')
```

### 1.2 表达矩阵

```R
eset <- gset[[1]]
expmat <- exprs(eset)

## 检查数据是否已经经过log转换，并进行log2转换
ex <- expmat
qx <- as.numeric(quantile(ex, c(0., 0.25, 0.5, 0.75, 0.99, 1.0), na.rm=T))

LogC <- (qx[5] > 100) ||
  (qx[6]-qx[1] > 50 && qx[2] > 0) ||
  (qx[2] > 0 && qx[2] < 1 && qx[4] > 1 && qx[4] < 2)

if (LogC) { ex[which(ex <= 0)] <- NaN
expmat <- log2(ex)
print("log2 transform finished")}else{print("log2 transform not needed")}
# [1] "log2 transform finished"

fit <- lmFit(expmat, design)
fit1 <- contrasts.fit(fit, contrast.matrix) 
fit2 <- eBayes(fit, 0.01)

DEGstb <- topTable(fit2, coef=1, n=Inf)
DEGstb <- DEGstb[order(DEGstb[,4], decreasing = F),]
```

before:

![](https://upload-images.jianshu.io/upload_images/14383117-ffe8f32b41027d61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

after:

![](https://upload-images.jianshu.io/upload_images/14383117-03e2a265ecd78a07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.3 设计矩阵和对比矩阵

```R
pdata <- pData(eset)
group_list <- pdata$`bone relapses (1=yes, 0=no):ch1`
library(limma)
design <- model.matrix(~0+factor(group_list))
colnames(design) <- c("free", "relapses")
rownames(design) <- colnames(expmat)
contrast.matrix <- makeContrasts('free-relapses',levels = design) 
```

### 1.4 差异分析

```R
fit <- lmFit(expmat, design)
fit1 <- contrasts.fit(fit, contrast.matrix) 
fit2 <- eBayes(fit1)
DEGstb <- topTable(fit2, coef=1, n=Inf)
DEGmtx <- na.omit(DEGstb)
head(DEGmtx)
#                  logFC   AveExpr         t      P.Value    adj.P.Val
# 209835_x_at  0.7010262 10.616209  6.251930 1.460149e-09 3.253649e-05
# 212014_x_at  0.7264678 10.372145  5.927511 8.785625e-09 9.788505e-05
# 204490_s_at  0.4928252  9.788234  5.558565 6.206262e-08 4.609804e-04
# 209380_s_at -0.5373120 10.062837 -5.421342 1.253664e-07 6.983850e-04
# 209831_x_at  0.2666780 10.594035  5.228288 3.295703e-07 1.338433e-03
# 204489_s_at  0.5619588 10.302607  5.181790 4.142994e-07 1.338433e-03
#                     B result
# 209835_x_at 11.154503     UP
# 212014_x_at  9.526820     UP
# 204490_s_at  7.757191     UP
# 209380_s_at  7.121927   DOWN
# 209831_x_at  6.249843    NOT
# 204489_s_at  6.043635     UP
```

### 1.5 火山图

```R
logFC_cutoff <- with(DEGmtx, mean(abs(logFC)) + 
                       2*sd(abs(logFC)))

DEGdf$result <- ifelse(DEGdf$P.Value < 0.05 &
                                    abs(DEGdf$logFC) > logFC_cutoff,
                                  ifelse(DEGdf$logFC >logFC_cutoff,
                                         "UP", "DOWN"), "NOT")

title <- paste0('Cutoff for logFC is ',round(logFC_cutoff,3), 
                '\nThe number of up gene is ',nrow(DEGmtx[DEGmtx$result =='UP',]) ,
                '\nThe number of down gene is ',nrow(DEGmtx[DEGmtx$result =='DOWN',]))

library(ggplot2)
ggplot(data=DEGmtx, aes(x=logFC, y=-log10(P.Value), color=result)) +
  geom_point(alpha=0.4, size=1.75) +
  theme_set(theme_set(theme_bw(base_size=20))) +
  xlab("log2 fold change") + ylab("-log10 p-value") +
  ggtitle( title ) + theme(plot.title = element_text(size=15,hjust = 0.5)) +
  scale_colour_manual(values = c('blue','black','red'))
```

![](https://upload-images.jianshu.io/upload_images/14383117-fcd865e3da478bd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.6 PCA图

```R
pca_data <- prcomp(t(expmat),scale=TRUE)
pcx <- data.frame(pca_data$x)
pcr <- cbind(samples=rownames(pcx),group_list, pcx) 
ggplot(pcr, aes(PC1, PC2)) + geom_point(size=5, aes(color = group_list)) +
  geom_text(aes(label="samples"),hjust=-0.1, vjust=-0.3)
```

这个数据中的分组：bone relapses: 1=yes, 0=no

分别是加样本名和不加样本名的亚子：

![](https://upload-images.jianshu.io/upload_images/14383117-c7ec0647332190f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.7 热图

选择&调整矩阵，设置各种参数：

```R
chosedgene <- rownames(DEGmtx)[1:50]
chosedmatx <- expmat[chosedgene,]      

re <- subset(pdata,pdata[,28]==1)
re <- row.names(re)
fr <- row.names(subset(pdata,pdata[,28]==0))
sampleorder <- as.vector(c(fr,re))

do_mtx <- t(scale(t(chosedmatx)))
do_mtx[do_mtx > 2] = 2
do_mtx[do_mtx < -2] = -2

do_mtx <- do_mtx[,sampleorder]

chosedprob <- row.names(do_mtx)
chosedsymbol <- AnnotationDbi::select(hgu133a.db, chosedprob,
                                      "SYMBOL",
                                      "PROBEID")[,2]
row.names(do_mtx) <- chosedsymbol

anno_col <- data.frame(sample=factor(rep(c("free", "relapses"), c(217, 69))))
row.names(anno_col) <- colnames(do_mtx)
ann_color <- list(sample=c(free = "#3a5fcd", relapses = "#cd0000"))
```

画图：

```r
png(filename = "GSE2034_hm_nmlmatrix_nocluscol.png", height = 5000, width = 6000,
   res = 500, units = "px")

pheatmap::pheatmap(do_mtx, scale = "none", color = color,
                   annotation_col = anno_col,
                   annotation_colors = ann_color,
                   fontsize = 8, fontsize_row = 8, 
                   show_colnames = F,
                   cluster_cols = F,
                   main = "GSE2034: free vs relapses HEATMAP (delected outliers)",)
dev.off()
```

![](https://upload-images.jianshu.io/upload_images/14383117-51525876f292f691.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

## 2. topGO 

### 2.1 topGO: Predefined list of interesting genes 

过表征分析 (over representation analysis, ORA)

> Tests based on gene counts. This is the most popular family of tests, given that it only requires the presence of a list of interesting genes and nothing more. Tests like Fisher's exact test, Hypegeometric test and binomial test belong to this family. Draghici et al. (2006) .

#### 2.1.1 构建 `topGOdata` 对象

构建 `geneID2GO` list

```R
library(topGO)
library(hgu133a.db)
library(dplyr)

probe_id <- row.names(expmat)
entrez_id <- AnnotationDbi::select(hgu133a.db,
                                   probe_id,
                                   "ENTREZID",
                                   "PROBEID")[,2]

geneID2GOtb <- AnnotationDbi::select(hgu133a.db, entrez_id,
                                     "GO", "ENTREZID")
geneID2GOsb <- geneID2GOtb %>%
  group_by(ENTREZID) %>%
  summarise(GO=list(GO))
geneID2GO <- geneID2GOsb$GO
names(geneID2GO) <- rownames(geneID2GOsb)
```

![](https://upload-images.jianshu.io/upload_images/14383117-724bc2c637312363.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建 `geneList`

```R
myInterestingDEG <- subset(DEGdf, abs(DEGdf$logFC) > 1)
myInterestingDEG <- subset(myInterestingDEG, myInterestingDEG$adj.P.Val < 0.05)
```

|             | logFC    | AveExpr  | t        | P.Value  | adj.P.Val | B        | result |
| ----------- | -------- | -------- | -------- | -------- | --------- | -------- | ------ |
| 214777_at   | 1.621019 | 7.578387 | 4.723681 | 3.63E-06 | 0.005771  | 4.093824 | UP     |
| 216365_x_at | 1.340734 | 5.631291 | 4.599642 | 6.35E-06 | 0.007075  | 3.592027 | UP     |
| 217157_x_at | 1.02288  | 8.543867 | 4.564419 | 7.43E-06 | 0.007348  | 3.45161  | UP     |
| 216984_x_at | 1.482014 | 7.826338 | 4.56344  | 7.46E-06 | 0.007348  | 3.447722 | UP     |
| 217378_x_at | 1.740957 | 8.055472 | 4.500623 | 9.85E-06 | 0.008778  | 3.199637 | UP     |
| 217258_x_at | 1.217204 | 7.180579 | 4.485677 | 1.05E-05 | 0.009013  | 3.14105  | UP     |
| 217148_x_at | 1.236926 | 9.662857 | 4.468183 | 1.14E-05 | 0.009236  | 3.072682 | UP     |
| 211798_x_at | 1.129989 | 7.765948 | 4.411513 | 1.45E-05 | 0.009236  | 2.852795 | UP     |
| 214669_x_at | 1.146124 | 11.22506 | 4.382578 | 1.65E-05 | 0.009236  | 2.741463 | UP     |
| 211644_x_at | 1.51252  | 8.895729 | 4.353739 | 1.86E-05 | 0.00958   | 2.631127 | UP     |
| 215176_x_at | 1.554616 | 9.781743 | 4.350516 | 1.89E-05 | 0.00958   | 2.618838 | UP     |
| 211637_x_at | 1.319264 | 6.128292 | 4.291975 | 2.42E-05 | 0.01036   | 2.396959 | UP     |
| 215121_x_at | 1.279395 | 12.98246 | 4.247872 | 2.92E-05 | 0.011018  | 2.231533 | UP     |
| 216576_x_at | 1.355247 | 8.468197 | 4.198257 | 3.59E-05 | 0.011951  | 2.047218 | UP     |
| 204540_at   | -1.06758 | 8.610066 | -4.19044 | 3.71E-05 | 0.011982  | 2.018367 | DOWN   |
| 216401_x_at | 1.475812 | 7.711042 | 4.180749 | 3.86E-05 | 0.011983  | 1.982631 | UP     |
| 207430_s_at | -1.3399  | 6.097727 | -4.15096 | 4.37E-05 | 0.012368  | 1.873294 | DOWN   |
| 213201_s_at | -1.27114 | 6.641464 | -4.13499 | 4.66E-05 | 0.01267   | 1.814941 | DOWN   |
| 215214_at   | 1.239997 | 7.376844 | 4.125793 | 4.84E-05 | 0.012763  | 1.781441 | UP     |
| 204933_s_at | 1.073296 | 4.540879 | 4.031533 | 7.10E-05 | 0.015467  | 1.441822 | UP     |
| 217281_x_at | 1.355866 | 5.417214 | 4.015399 | 7.58E-05 | 0.015784  | 1.384391 | UP     |
| 214858_at   | -1.08985 | 6.455748 | -4.00976 | 7.75E-05 | 0.015861  | 1.36435  | DOWN   |
| 216510_x_at | 1.132241 | 6.304655 | 3.99392  | 8.26E-05 | 0.016195  | 1.308245 | UP     |
| 215379_x_at | 1.123251 | 11.77926 | 3.943786 | 0.000101 | 0.016689  | 1.131931 | UP     |
| 211634_x_at | 1.014891 | 6.92074  | 3.93688  | 0.000104 | 0.016689  | 1.107799 | UP     |
| 210297_s_at | -1.201   | 6.705364 | -3.93263 | 0.000105 | 0.016689  | 1.092959 | DOWN   |
| 209138_x_at | 1.136452 | 13.28617 | 3.9291   | 0.000107 | 0.016689  | 1.080658 | UP     |
| 211645_x_at | 1.244501 | 9.308405 | 3.889339 | 0.000125 | 0.017262  | 0.942694 | UP     |
| 216491_x_at | 1.292163 | 5.893802 | 3.845647 | 0.000148 | 0.018642  | 0.79254  | UP     |
| 216560_x_at | 1.278329 | 5.810675 | 3.778756 | 0.000192 | 0.020615  | 0.565602 | UP     |
| 221286_s_at | 1.10116  | 7.542805 | 3.777608 | 0.000192 | 0.020615  | 0.561739 | UP     |
| 206391_at   | 1.082072 | 6.946425 | 3.505906 | 0.000528 | 0.031189  | -0.32275 | UP     |
| 205509_at   | -1.20626 | 8.869439 | -3.48369 | 0.000572 | 0.032322  | -0.39241 | DOWN   |
| 209374_s_at | 1.034312 | 10.40725 | 3.443591 | 0.000659 | 0.034236  | -0.51714 | UP     |
| 204623_at   | -1.04503 | 11.21622 | -3.25463 | 0.001271 | 0.045188  | -1.08699 | DOWN   |

```R
geneNames <- names(geneID2GO) 
myInterestingGenestb <- AnnotationDbi::select(hgu133a.db, row.names(myInterestingDEG),
                                            "ENTREZID", "PROBEID")

myInterestingGenes <- geneNames[match(myInterestingGenestb[,2], 
                                      geneNames)]
myInterestingGenes <- na.omit(myInterestingGenes)

ORAgeneList <- factor(as.integer(geneNames %in% myInterestingGenes)) 
names(ORAgeneList) <- geneNames 
str(ORAgeneList) 
#  Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
#  - attr(*, "names")= chr [1:13755] "10" "100" "1000" "10000" ...
```

构建 `topGOdata` 对象：

```r
ORAGOdata <- new("topGOdata",
              ontology = "BP",
              allGenes = ORAgeneList,
              annot = annFUN.gene2GO,
              gene2GO = geneID2GO)
# 
# Building most specific GOs .....
# 	( 11302 GO terms found. )
# 
# Build GO DAG topology ..........
# 	( 15326 GO terms and 36394 relations. )
# 
# Annotating nodes ...............
# 	( 11973 genes annotated to the GO terms. )
ORAGOdata
# 
# ------------------------- topGOdata object -------------------------
# 
#  Description:
#    -   
# 
#  Ontology:
#    -  BP 
# 
#  13755 available genes (all genes from the array):
#    - symbol:  10 100 1000 10000 100008586  ...
#    - 29  significant genes. 
# 
#  11973 feasible genes (genes that can be used in the analysis):
#    - symbol:  10 100 1000 10000 10001  ...
#    - 21  significant genes. 
# 
#  GO graph (nodes with at least  1  genes):
#    - a graph with directed edges
#    - number of nodes = 15326 
#    - number of edges = 36394 
# 
# ------------------------- topGOdata object -------------------------
# 
```

#### 2.1.2 富集分析

> we perform a classical enrichment analysis by testing the over-representation of GO terms within the group of di erentially expressed genes. 

```R
ORAresultFisher <- runTest(ORAGOdata, algorithm = "classic", statistic = "fisher") 
# 
# 			 -- Classic Algorithm -- 
# 
# 		 the algorithm is scoring 449 nontrivial nodes
# 		 parameters: 
# 			 test statistic: fisher
# 
ORAresultFisher
# 
# Description:  
# Ontology: BP 
# 'classic' algorithm with the 'fisher' test
# 15326 GO terms scored: 86 terms with p < 0.01
# Annotation data:
#     Annotated genes: 11973 
#     Significant genes: 21 
#     Min. no. of genes annotated to a GO: 1 
#     Nontrivial nodes: 449 
```

使用 `topGO` 包时，所构建的 `topGOdata` 包含了 gene universe (ORAGOdata) 和 p-value (GSEAGOdata), ORA 和 GSEA 对应的统计方法可以**同时**应用于**同一个** `topGOdata` 对象。所以区别只是基于不同的已知信息，构建 `topGOdata` 的方法不同。（maybe?

基于 **classic** 和 **elim** 算法，对 `ORAdata` 进行 Kolmogorov-Smirnov test:

```R
ORAresultKS <- runTest(ORAGOdata, algorithm = "classic", statistic = "ks") 
# 
# 			 -- Classic Algorithm -- 
# 
# 		 the algorithm is scoring 15326 nontrivial nodes
# 		 parameters: 
# 			 test statistic: ks
# 			 score order: increasing
ORAresultKS
# 
# Description:  
# Ontology: BP 
# 'classic' algorithm with the 'ks' test
# 15326 GO terms scored: 903 terms with p < 0.01
# Annotation data:
#     Annotated genes: 11973 
#     Significant genes: 21 
#     Min. no. of genes annotated to a GO: 1 
#     Nontrivial nodes: 15326 
```

```R
ORAresult.elim <- runTest(ORAGOdata, algorithm = "elim", statistic = "ks") 
# 
# 			 -- Elim Algorithm -- 
# 
# 		 the algorithm is scoring 15326 nontrivial nodes
# 		 parameters: 
# 			 test statistic: ks
# 			 cutOff: 0.01
# 			 score order: increasing
# ...
ORAresult.elim
# 
# Description:  
# Ontology: BP 
# 'elim' algorithm with the 'ks : 0.01' test
# 15326 GO terms scored: 291 terms with p < 0.01
# Annotation data:
#     Annotated genes: 11973 
#     Significant genes: 21 
#     Min. no. of genes annotated to a GO: 1 
#     Nontrivial nodes: 15326 
```

#### 2.1.3 Summary

```R
ORAallRes <- GenTable(ORAGOdata,classicFisher = ORAresultFisher,
                   classicKS = ORAresultKS,
                   elimKS = ORAresult.elim,
                   orderBy = "elimKS",
                   ranksOf = "classicFisher",
                   topNodes = 20)
```

|      | GO.ID      | Term                                        | Annotated | Significant | Expected | Rank in classicFisher | classicFisher | classicKS | elimKS   |
| ---- | ---------- | ------------------------------------------- | --------- | ----------- | -------- | --------------------- | ------------- | --------- | -------- |
| 1    | GO:0030198 | extracellular matrix organization           | 303       | 1           | 0.53     | 233                   | 0.42          | 2.20E-11  | 4.10E-08 |
| 2    | GO:0019886 | antigen processing and presentation of e... | 88        | 0           | 0.15     | 449                   | 1             | 5.50E-08  | 5.50E-08 |
| 3    | GO:0098664 | G protein-coupled serotonin receptor sig... | 23        | 0           | 0.04     | 450                   | 1             | 7.60E-08  | 7.60E-08 |
| 4    | GO:0006120 | mitochondrial electron transport, NADH t... | 42        | 0           | 0.07     | 451                   | 1             | 2.10E-07  | 2.10E-07 |
| 5    | GO:0007190 | activation of adenylate cyclase activity    | 29        | 0           | 0.05     | 452                   | 1             | 5.10E-07  | 5.10E-07 |
| 6    | GO:0060337 | type I interferon signaling pathway         | 87        | 0           | 0.15     | 453                   | 1             | 3.70E-06  | 3.70E-06 |
| 7    | GO:0007193 | adenylate cyclase-inhibiting G protein-c... | 63        | 0           | 0.11     | 454                   | 1             | 1.70E-08  | 4.80E-06 |
| 8    | GO:0006069 | ethanol oxidation                           | 9         | 0           | 0.02     | 455                   | 1             | 4.90E-06  | 4.90E-06 |
| 9    | GO:0060078 | regulation of postsynaptic membrane pote... | 107       | 0           | 0.19     | 456                   | 1             | 5.60E-07  | 5.70E-06 |
| 10   | GO:0007155 | cell adhesion                               | 1134      | 0           | 1.99     | 457                   | 1             | 1.30E-06  | 6.20E-06 |
| 11   | GO:0042531 | positive regulation of tyrosine phosphor... | 54        | 0           | 0.09     | 458                   | 1             | 6.60E-06  | 6.60E-06 |
| 12   | GO:0000184 | nuclear-transcribed mRNA catabolic proce... | 114       | 0           | 0.2      | 459                   | 1             | 9.90E-06  | 9.90E-06 |
| 13   | GO:0048013 | ephrin receptor signaling pathway           | 81        | 0           | 0.14     | 460                   | 1             | 1.10E-05  | 1.10E-05 |
| 14   | GO:0006749 | glutathione metabolic process               | 43        | 0           | 0.08     | 461                   | 1             | 1.40E-05  | 1.40E-05 |
| 15   | GO:0006123 | mitochondrial electron transport, cytoch... | 13        | 0           | 0.02     | 462                   | 1             | 1.40E-05  | 1.40E-05 |
| 16   | GO:0009636 | response to toxic substance                 | 446       | 0           | 0.78     | 463                   | 1             | 1.00E-08  | 1.70E-05 |
| 17   | GO:1901687 | glutathione derivative biosynthetic proc... | 19        | 0           | 0.03     | 464                   | 1             | 2.70E-05  | 2.70E-05 |
| 18   | GO:1901701 | cellular response to oxygen-containing c... | 924       | 1           | 1.62     | 335                   | 0.82          | 3.30E-06  | 3.20E-05 |
| 19   | GO:1904322 | cellular response to forskolin              | 10        | 0           | 0.02     | 465                   | 1             | 3.20E-05  | 3.20E-05 |
| 20   | GO:0097267 | omega-hydroxylase P450 pathway              | 9         | 0           | 0.02     | 466                   | 1             | 3.50E-05  | 3.50E-05 |

### 2.2 topGO: Using the genes score 

基因富集分析 (gene set enrichment analysis, GSEA)

> Tests based on gene scores or gene ranks. It includes Kolmogorov-Smirnov like tests (also known as GSEA), Gentleman's Category, t-test, etc. Ackermann and Strimmer (2009).

#### 2.2.1 构建 `topGOdata` 对象

构建 `geneList`

```R
GSEAgeneList <- subset(DEGdf, select = "adj.P.Val")
GSEAgeneList <- as.numeric(GSEAgeneList[,1])
names(GSEAgeneList) <- row.names(DEGdf)
str(GSEAgeneList)
#  Named num [1:22283] 3.25e-05 9.79e-05 4.61e-04 6.98e-04 1.34e-03 ...
#  - attr(*, "names")= chr [1:22283] "209835_x_at" "212014_x_at" "204490_s_at" "209380_s_at" ...
```

通过函数 `topDiffGenes()` 筛选q值小于0.01的基因。

```r
topDiffGenes <- function(allScore) {
  return(allScore < 0.01)
}
length(GSEAgeneList)
# [1] 22283
sum(topDiffGenes(GSEAgeneList))
# [1] 50
```

构建 `topGOdata` 对象：

```R
GSEAGOdata <- new("topGOdata",
                  description = "GO analysis of GSE2034 data; free vs relapses",
                  ontology = "BP",
                  allGenes = GSEAgeneList,
                  geneSel = topDiffGenes,
                  nodeSize = 5,
                  annot = annFUN.db,
                  affyLib = "hgu133a.db")
# 
# Building most specific GOs .....
# 	( 11302 GO terms found. )
# 
# Build GO DAG topology ..........
# 	( 15326 GO terms and 36394 relations. )
# 
# Annotating nodes ...............
# 	( 19375 genes annotated to the GO terms. )

GSEAGOdata
# ------------------------- topGOdata object -------------------------
# 
#  Description:
#    -  GO analysis of GSE2034 data; free vs relapses 
# 
#  Ontology:
#    -  BP 
# 
#  22283 available genes (all genes from the array):
#    - symbol:  209835_x_at 212014_x_at 204490_s_at 209380_s_at 209831_x_at  ...
#    - score :  3.25364921e-05 9.78850462e-05 0.000460980422 0.000698385002 0.0013384333  ...
#    - 50  significant genes. 
# 
#  19375 feasible genes (genes that can be used in the analysis):
#    - symbol:  209835_x_at 212014_x_at 204490_s_at 209380_s_at 209831_x_at  ...
#    - score :  3.25364921e-05 9.78850462e-05 0.000460980422 0.000698385002 0.0013384333  ...
#    - 47  significant genes. 
# 
#  GO graph (nodes with at least  5  genes):
#    - a graph with directed edges
#    - number of nodes = 10532 
#    - number of edges = 24696 
# 
# ------------------------- topGOdata object -------------------------
# 
```

#### 2.2.2 富集分析



#### 2.2.3 Summary

`GenTable` 导出表格





未完不更





## References 

- <https://davetang.org/muse/2018/05/15/making-a-heatmap-in-r-with-the-pheatmap-package/>

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
