> 实在懒得找meme了
![](https://upload-images.jianshu.io/upload_images/14383117-f523fc5dd04cf591.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# GOrilla, clusterProfiler, topGO 分析 ranked gene list (GSEA) 对比 

## Genelist preparation

保证了三个工具输入的基因是一致的。

```r
# genelist preparation
library(hgu133plus2.db)
rankedprobe <- row.names(DEGdf)
load("GSE76275_DEGs.Rdata")
## ranked entrezid by pval
rankedentrezt <- AnnotationDbi::select(hgu133plus2.db,
                                      rankedprobe,
                                      "ENTREZID",
                                      "PROBEID")
rankedentrez <- unique(na.omit(rankedentrezt$ENTREZID))
```

### for *GOrilla*

纠正一个误解，之前以为 *GOrilla* 要求的 gene list 是按表达量从高到低排序。

> This is particularly useful in many typical cases where genomic data may be naturally represented as a ranked list of genes (e.g. by level of expression or of differential expression). 

但实际上是依然是基于t检验，也就是基于p值。 

> All genes were ranked according to how well they differentiate between the two groups using a simple t-test. The top of the list contained the genes that were the best separators between the two groups. 

*GOrilla* 的优势主要体现在：

- 与基于超几何分布的工具相比——阈值灵活
- 与其他 阈值灵活/类似GSEA/利用Kolmogorov-Smirnov检验 的工具相比：
  - 专注于GO，可直接生成DAG
  - 利用 "mHG " 可得到精确的 p.val
  - 速度快
  - （似乎放在现在这些都不是什么问题，毕竟 GSEA 2005年发布，*GOrilla* 2008年发布...不过一布拿到图+表也是很爽的额 (。﹏。)

```R
## ranked symbol (increasing by pval)
rankedsymbol <- AnnotationDbi::select(hgu133plus2.db,
                                       rankedentrez,
                                       "SYMBOL",
                                       "ENTREZID")[,2]
save(rankedsymbol, file = "rankedsymbol_pvbased_forGOrilla.Rdata")
write.table(rankedsymbol, file = 
              "rankedsymbol_pvbased_forGOrilla.txt",
            quote = F, col.names = F, row.names = F)
```

手动选择 pvalue cutoff

![](https://upload-images.jianshu.io/upload_images/14383117-9ef4602d458485b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### for clusterProfiler

```R
## ranked entrezid with pval (increasing by pval)
matchedprobe <- match(rankedentrez,
                      rankedentrezt$ENTREZID)
matchedprobe2 <- rankedentrezt[matchedprobe,][,"PROBEID"]
respon.pval <- DEGdf[matchedprobe2,]$P.Value
entrezlist_pval <- respon.pval
names(entrezlist_pval) <- rankedentrez
save(entrezlist_pval, file = "genelist_ENTREZID_pVal.Rdata")

### decreasing entrezid list by pval
entrezlist_dcrpv <- sort(entrezlist_pval, decreasing = T)
save(entrezlist_dcrpv, file = "genelist_ENTREZID_Decr_pVal.Rdata")

### decreasing entrezid list by FC
entrezlist_FC <- DEGdf[matchedprobe2,]$logFC
names(entrezlist_FC) <- rankedentrez
entrezlist_FC <- sort(entrezlist_FC, decreasing = T)
save(entrezlist_FC, file = "genelist_ENTREZID_FC.Rdata")
```

### for topGO 

```R
## probeid with p.val list (increasing by pval)
probelist_pval <- respon.pval
names(probelist_pval) <- matchedprobe2
save(probelist_pval, file = "genelist_PROBEID_pVal.Rdata")
```

## *GOrilla* 

### Running mode: Single ranked list of genes

很酷炫的一张**大**图

![](https://upload-images.jianshu.io/upload_images/14383117-e6936e8cf569c028.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/14383117-916cb91302d3e556.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

重复基因会被自动删除

![](https://upload-images.jianshu.io/upload_images/14383117-222a2dddddf4ec3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但并不知道哪来的这12个 duplicate genes (+_+)?

```R
table(table(rankedsymbol))
# 
#     1 
# 22012 
```

> **Enrichment (N, B, n, b)** is defined as follows:
> N - is the total number of genes
> B - is the total number of genes associated with a specific GO term
> n - is the number of genes in the top of the user's input list or in the target set when appropriate
> b - is the number of genes in the intersection
> Enrichment = (b/n) / (B/N)

![](https://upload-images.jianshu.io/upload_images/14383117-ff5c49256816585e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

缺点是并没有显示富集到了多少个GO term, 强行扒下这个表后，小小操作：

```R
GOrillares <- read.csv("GOrillatable.txt", header = T, sep = "\t")
save(GOrillares, file = "GOrillatable.Rdata")
```

![](https://upload-images.jianshu.io/upload_images/14383117-a0f52899c3a7bda5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以就是有100个 term.

```R
nrow(GOrillares)
# [1] 100
```

## clusterProfiler 

### GSEA GO-BP

标准的 GSEA.

关于输入的 gene list, y叔是这么说的：

> The `geneList` contains three features:
>
> 1. numeric vector: fold change or other type of numerical variable
> 2. named vector: every number was named by the corresponding gene ID
> 3. sorted vector: number should be sorted in decreasing order

但用 p.val 的效果似乎一般。

```R
## clusterProfiler GSEA GO-BP
library(clusterProfiler)
library(hgu133plus2.db)
load("genelist_ENTREZID_Decr_pVal.Rdata")

### with decreasing p.val
gseaGO_BP <- gseGO(geneList     = entrezlist_dcrpv,
                   OrgDb        = hgu133plus2.db,
                   keyType      = "ENTREZID",
                   ont          = "BP",
                   nPerm        = 1000,   ## 排列数
                   minGSSize    = 5,
                   maxGSSize    = 500,
                   pvalueCutoff = 0.95,
                   verbose      = TRUE)  ## 不输出结果
gseaGO_BPresult <- gseaGO_BP@result
save(gseaGO_BPresult, file = "cp_gseaGO_BPresult.Rdata")
```

可能和网络也有关系？几次输入同样的参数，得到的结果并不一样....有时是报错 no term, 迷惑desu

![](https://upload-images.jianshu.io/upload_images/14383117-9567e31918bf40f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/14383117-22c9461a2422d778.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也没什么显著的 term

![](https://upload-images.jianshu.io/upload_images/14383117-c6d396cb66815f82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还是像示例里那样用 decreasing FC 

```r
### with decreasing FC
load("genelist_ENTREZID_FC.Rdata")
gseaGO_BP2 <- gseGO(geneList     = entrezlist_FC,
                    OrgDb        = hgu133plus2.db,
                    keyType      = "ENTREZID",
                    ont          = "BP",
                    nPerm        = 1000,   ## 排列数
                    minGSSize    = 5,
                    maxGSSize    = 500,
                    pvalueCutoff = 0.05,
                    verbose      = TRUE)  ## 不输出结果
gseaGO_BPresult2 <- gseaGO_BP2@result
save(gseaGO_BPresult2, file = "cp_gseaGO_BPresult2.Rdata")
```

结果果然正常多了

![](https://upload-images.jianshu.io/upload_images/14383117-8e6bf3b718055265.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## topGO 

### GSEA-like GO-BP (Using the genes score )

准确地说，也许不能直接叫做 GSEA 法。topGO 说明书里的说法是：

> We will use two types of test statistics: Fisher's exact test which is based on gene counts, and a Kolmogorov-Smirnov like test which computes enrichment based on gene scores. 

并且：

> We can use both these tests since each gene has a score (representing how di erentially expressed a gene is) and by the means of topDiffGenes functions the genes are categorized into di erentially expressed or not di erentially expressed genes.

正因为有 `topDiffGenes` 这个函数的存在，相当于无形中给了 target list, 而 `allGenes = `  相当于 universe gene list. 所以这也许是 `topGO` 无论用类似 ORA 或 类似 GSEA 法构建的 `topGOdata` ，算法和检验方法可以各种混用的原因。(maybe <@_@>

![](https://upload-images.jianshu.io/upload_images/14383117-ab474b6f0e2e5e11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
## topGO GSEA GO-BP
library(topGO)
library(hgu133plus2.db)
load("genelist_PROBEID_pVal.Rdata")

## function: topDiffGenes
topDiffGenes <- function(allScore) {
  return(allScore < 0.01)   ## p.val < 0.01
}
sum(topDiffGenes(probelist_pval))
# [1] 12210

## construct a topGOdata object
GSEAGOdata_BP <- new("topGOdata",
                     ontology = "BP",
                     allGenes = probelist_pval,
                     geneSel = topDiffGenes,
                     nodeSize = 5,
                     annot = annFUN.db,
                     affyLib = "hgu133plus2.db")
```

```R
## Kolmogorov-Smirnov like test - classic
KSres <- runTest(GSEAGOdata_BP, algorithm = "weight01", statistic = "ks") 
KSres
# 
# Description:  
# Ontology: BP 
# 'weight01' algorithm with the 'ks' test
# 9106 GO terms scored: 203 terms with p < 0.01
# Annotation data:
#     Annotated genes: 16454 
#     Significant genes: 9542 
#     Min. no. of genes annotated to a GO: 5 
#     Nontrivial nodes: 9106 
GSEA_BPres <- GenTable(GSEAGOdata_BP,
                       weight01KS = KSres,
                       orderBy = "weight01KS",
                       ranksOf = "weight01KS",
                       topNodes = 203)
save(GSEA_BPres, file = "topGO_gseaGO_BPresult.Rdata")
```

![](https://upload-images.jianshu.io/upload_images/14383117-c592b9801d4647d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 结果对比

先找出三种工具结果中共有的 GO term:

```R
nrow(GOrillares)  ## GOrilla
# [1] 100
nrow(gseaGO_BPresult2)  ## clusterProfiler
# [1] 457
nrow(GSEA_BPres)  ## topGO
# [1] 203
```

```R
GOrillavscp <- intersect(GOrillares$GO.term, gseaGO_BPresult2$ID)
GOrillavstop <- intersect(GOrillares$GO.term, GSEA_BPres$GO.ID)
triple <- intersect(GOrillavscp, GOrillavstop)
triple
# [1] "GO:0050867"
```

只有一个😳

```R
row.names(GOrillares) <- GOrillares$GO.term
row.names(GSEA_BPres) <- GSEA_BPres$GO.ID
theonly <- cbind(GOrillares[triple,],
                 gseaGO_BPresult2[triple,],
                 GSEA_BPres[triple,])
finetable <- subset(theonly, select = c("Description",
                    "Enrichment..N..B..n..b.", "P.value","FDR.q.value",
                    "setSize","enrichmentScore","pvalue", "p.adjust","qvalues", 
                    "Annotated", "Significant","weight01KS"))
finetable
#                                       Description Enrichment..N..B..n..b.
# GO:0050867 positive regulation of cell activation  35.01 (17224,328,12,8)
#             P.value FDR.q.value setSize enrichmentScore     pvalue
# GO:0050867 7.67e-11    6.28e-08     312       0.2948657 0.00203252
#              p.adjust    qvalues Annotated Significant weight01KS
# GO:0050867 0.04044123 0.03411746       313         174    0.00383
```

![](https://upload-images.jianshu.io/upload_images/14383117-c70247bdb0872439.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看起来， *GOrilla* 的结果和常用的两个R包的结果差的还是挺多的😲

### 出于好奇看一下 topGO 和 clusterProfiler 结果的差别

```R
topvscp <- intersect(GSEA_BPres$GO.ID, gseaGO_BPresult2$ID)
length(topvscp)
# [1] 19
```

![](https://upload-images.jianshu.io/upload_images/14383117-c8e82831b0d6fe69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

大概就是这个亚子。

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
