> ![](https://upload-images.jianshu.io/upload_images/14383117-716371c59e87fe1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关于三个工具的具体描述（尤其*GOrilla*）,见 [GO富集分析-对比Gorilla, clusterProfiler, topGO三种工具part1 ~ GSEA 篇]([https://www.jianshu.com/p/a2e4eefd606d](https://www.jianshu.com/p/a2e4eefd606d)
)

# Interesting gene vs universe~  (ORA )对比 

## Gene list preparation
保证输入一致， *GOrilla* 输入 symbol, clusterProfiler 及 topGO 输入 ENTREZ ID.

```R
# gene list preparation for ORA
# GSE76275

library(hgu133plus2.db)
library(magrittr)
load("GSE76275_DEGs.Rdata")

# universe gene list
universeprobe <- row.names(DEGdf)
universeentrez <-  AnnotationDbi::select(hgu133plus2.db,
                                         universeprobe,
                                         "ENTREZID",
                                         "PROBEID")[,2] %>%
  na.omit()%>%
  unique()
save(universeentrez, file = "universe_entrezidlist.Rdata") 

### for GOrilla
universesymbol <- AnnotationDbi::select(hgu133plus2.db,
                                        universeentrez,
                                        "SYMBOL",
                                        "ENTREZID")[,2] %>%
  na.omit()%>%
  unique()
write.table(universesymbol, file = "universe_symbollist.txt",
            quote = F, col.names = F, row.names = F)

## interesting gene list
interesprobe <- row.names(DEGdf[abs(DEGdf$logFC) > 1,])
interesentrez <- AnnotationDbi::select(hgu133plus2.db,
                                       interesprobe,
                                       "ENTREZID",
                                       "PROBEID")[,2] %>%
  na.omit()%>%
  unique()
save(interesentrez, file = "interesting_entrezidlist.Rdata") 


### for GOrilla
interessymbol <- AnnotationDbi::select(hgu133plus2.db,
                                       interesentrez,
                                       "SYMBOL",
                                       "ENTREZID")[,2] %>%
  na.omit()%>%
  unique()
write.table(interessymbol, file = "interesting_symbollist.txt",
            quote = F, col.names = F, row.names = F)
```

## clusterProfiler 

### ORA GO-BP

```R
# clusterProfiler ORA
## GO-BP
orago_BP <- enrichGO(gene          = interesentrez,
                     universe      = universeentrez,
                     OrgDb         = hgu133plus2.db,
                     ont           = "BP",
                     pAdjustMethod = "BH",
                     pvalueCutoff  = 0.05,
                     qvalueCutoff  = 0.05,
                     readable      = TRUE)
cpORAGOres_BP <- orago_BP@result
dim(cpORAGOres_BP)
# [1] 2001    9
save(cpORAGOres_BP, file = "cpORAGOres_BP.Rdata")
```

## topGO 

### predefined list of interesting genes 
topGO 需要先将输入的 genelist 和 GO 数据库匹配，基因数已经减少到了17762个。

construct a `geneID2GO` object (a list)

```R
### construct a geneID2GO object (a list)
library(dplyr)
geneID2GOtb <- na.omit(AnnotationDbi::select(hgu133plus2.db, 
                                             universeentrez,
                                             "GO", "ENTREZID"))

geneID2GOmg <- geneID2GOtb %>%
  group_by(ENTREZID) %>%
  summarise(GO=list(GO))
geneID2GO <- geneID2GOmg$GO
names(geneID2GO) <- geneID2GOmg$ENTREZID
```
构建一个表示在 universe gene 中，哪些基因是 interesting gene & 哪些不是的 factor，并用这个 factor 代表两个 gene list.

construct a `genelist` (a factor)

```R
### construct a genelist (a factor)
geneNames <- names(geneID2GO)
myInterestingGenes <- geneNames[match(interesentrez, geneNames)]
ORAgeneList <- factor(as.integer(geneNames %in% myInterestingGenes)) 
names(ORAgeneList) <- geneNames 
str(ORAgeneList) 
#  Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
#  - attr(*, "names")= chr [1:17762] "1" "10" "100" "1000" ...
```

```R
### enrichment analysis
ORAGOdata <- new("topGOdata",
                 ontology = "BP",
                 allGenes = ORAgeneList,
                 annot = annFUN.gene2GO,
                 gene2GO = geneID2GO)
ORAGOdata
# 
# ------------------------- topGOdata object -------------------------
# 
#  Description:
#    -   
# 
# Ontology:
#    -  BP 
# 
#  17762 available genes (all genes from the array):
#    - symbol:  1 10 100 1000 10000  ...
#    - 121  significant genes. 
# 
#  15869 feasible genes (genes that can be used in the analysis):
#   - symbol:  1 10 100 1000 10000  ...
#    - 111  significant genes. 
# 
#  GO graph (nodes with at least  1  genes):
#    - a graph with directed edges
#    - number of nodes = 15845 
#    - number of edges = 37664 
# 
# ------------------------- topGOdata object -------------------------
```
经典Fisher test:
```R
###  classic Fisher TEST
ORAresultFisher <- runTest(ORAGOdata, algorithm = "classic", statistic = "fisher") 
# 
# 			 -- Classic Algorithm -- 
# 
# 		 the algorithm is scoring 2826 nontrivial nodes
# 		 parameters: 
# 			 test statistic: fisher
ORAresultFisher
# 
# Description:  
# Ontology: BP 
# 'classic' algorithm with the 'fisher' test
# 15845 GO terms scored: 176 terms with p < 0.01
# Annotation data:
#     Annotated genes: 15869 
#     Significant genes: 111 
#     Min. no. of genes annotated to a GO: 1 
#     Nontrivial nodes: 2826 
```

```R
### summary table
ORAallRes <- GenTable(ORAGOdata,classicFisher = ORAresultFisher,
                      orderBy = "classicFshier",ranksOf = "classicFshier",
                      topNodes = 176)
save(ORAallRes, file = "topORAGOres_BP.Rdata")
```

## 结果对比

```R
# comparison 
## get GOrilla res
GOrillaORA <- read.csv("GOrillares_2lists.txt", header = T,
                       sep = "\t")

load("cpORAGOres_BP.Rdata")
load("topORAGOres_BP.Rdata")
```
可以看出在数量上差异也很明显。
```R
nrow(cpORAGOres_BP)
# [1] 2001
nrow(ORAallRes)
# [1] 176
nrow(GOrillaORA)
# [1] 12
```
取三个结果的交集，得到5个共有 GO term.
```R
cpvstop <- intersect(cpORAGOres_BP$ID,ORAallRes$GO.ID)
triple <- intersect(cpvstop, GOrillaORA$GO.term)

row.names(GOrillaORA) <- GOrillaORA$GO.term
row.names(ORAallRes) <- ORAallRes$GO.ID
thefive <- cbind(GOrillaORA[triple,], cpORAGOres_BP[triple,], ORAallRes[triple,])

finetable <- subset(thefive, select = c("Description",
                                        "P.value","FDR.q.value", "Enrichment..N..B..n..b.",   ## GOrilla
                                        "GeneRatio","BgRatio""pvalue", "p.adjust", "qvalue", ## cp
                                        "Annotated", "Significant", "classicFisher"))  ## topGO
```

![](https://upload-images.jianshu.io/upload_images/14383117-3efe9d9be1c8d8c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看出，针对ORA法，*GOrilla* 和 clusterProfiler 得到的 p.val 较为接近。（GSEA法，clusterProfiler 和 topGO 得到的 p.val 更接近。
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
