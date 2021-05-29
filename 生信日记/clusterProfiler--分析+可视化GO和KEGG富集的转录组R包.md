> 写作之友
![](https://upload-images.jianshu.io/upload_images/14383117-a073e5462460eaba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 基因和基因簇的功能谱 (functional profiles) 的统计学分析及可视化

> ***G Yu***, LG Wang, Y Han, QY He. clusterProfiler: an R package for comparing biological themes among gene clusters. ***OMICS: A Journal of Integrative Biology*** 2012, 16(5):284-287.doi:[10.1089/omi.2011.0118](http://dx.doi.org/10.1089/omi.2011.0118)

## 1. 关于 `clusterProfiler`

- 上面说的功能谱 (functional profiles) 就是 GO 和 KEGG.
- Jimmy大神说这可是个**神包**啊~

瞅一眼这个包里都有什么：

```R
library(clusterProfiler)
ls(package:clusterProfiler)
#  [1] "bitr"                 "bitr_kegg"           
#  [3] "browseKEGG"           "buildGOmap"          
#  [5] "cnetplot"             "compareCluster"      
#  [7] "dotplot"              "download_KEGG"       
#  [9] "dropGO"               "emapplot"            
# [11] "enrichDAVID"          "enricher"            
# [13] "enrichGO"             "enrichKEGG"          
# [15] "enrichMKEGG"          "geneID"              
# [17] "geneInCategory"       "Gff2GeneTable"       
# [19] "go2ont"               "go2term"             
# [21] "gofilter"             "goplot"              
# [23] "groupGO"              "GSEA"                
# [25] "gseaplot"             "gseGO"               
# [27] "gseKEGG"              "gseMKEGG"            
# [29] "gsfilter"             "heatplot"            
# [31] "idType"               "ko2name"             
# [33] "merge_result"         "plotGOgraph"         
# [35] "read.gmt"             "ridgeplot"           
# [37] "search_kegg_organism" "setReadable"         
# [39] "simplify"             "uniprot_get"   
```

## 2. 一些 ~~众所周知的~~ 术语

- Gene sets and pathway

- Gene Ontology (GO)

- Kyoto Encyclopedia of Genes and Genomes (KEGG)

  > 这里引用臺灣大學醫學研究部 (Department of Medical Research) **許家郎專案助理研究員** 《共同研究室電子報 第五十四期 JUN.10.2018》。
  >
  > > 基因集(gene set)指的是一群基因的集合，且這群基因具有類似的性質或功能。對於生醫學者最熟悉的基因集，莫過於生物路徑(biological pathway)，例如IGF signaling pathway，一個反應路徑是由一連串基因間的活化、抑制、或交互作用所組成，如果把所有參與這條生物路徑的基因集合起來，就可稱做基因集。目前線上已有許多生物路徑的資料庫，例如由京都大學Minoru Kanehisa教授於1995年創立的KEGG(Kyoto Encyclopedia of Genes and Genomes)，與由歐洲生物資訊研究所(EBI)建構的Reactome資料庫。這類型資料庫比較嚴謹，大多利用人工閱讀文獻，且兩基因間的調控關係需有明確實驗證據才會收錄，因此是很好的基因集來源，並且這些生物路徑資料庫通常會提供生物路徑，來呈現完整的調控關係，對於理解基因間的調控關係有很大的幫助。
  > >
  > > 另一個常見的基因集來自於Gene Ontology (GO) Consortium。GO主要目的是訂定一套標準詞彙(controlled vocabulary)，用來描述基因的功能。主要有三大分支，分別是Cellular component (CC)，Molecular function (MF)與Biological process (BP)。從字面上來看，CC中的詞(term)是用來描述指基因產物在細胞內外的位置，例如plasma membrane；MF則用來定義基因產物分子層次的功能，例如DNA binding；而BP是用來描述基因產物所參與的生物路徑或機制，例如cell death。


- Other gene sets
  包括但不限于：Disease Ontology (DO), Disease Gene Network (DisGeNET), wikiPathways, Molecular Signatures Database (MSigDb)

## 3. 功能富集分析方法

- 过表征分析 (over representation analysis, ORA)

- 基因富集分析 (gene set enrichment analysis, GSEA)

  > 引用源同上。
  >
  > > 基因集的分析策略可以分成兩類: over-representation analysis (ORA)與gene-set enrichment analysis (GSEA)。這兩種方法最大的差別是，ORA會先經過篩選，挑出我們有興趣的基因，而GSEA則不經過篩選基因的動作。以轉錄體資料為例，實驗設計上，通常會比較兩種狀態，並利用統計方法找出哪些基因具有「表現差異」，可能會設定統計檢定的p值或fold-change，來決定這是我們有興趣的基因，接著就針對這群基因做解讀。這樣篩選的過程，p值或fold-change如何設定才能抓出真正具有「生物意義」的基因，且這種方法把每個基因都視為同等重要，然而每個基因的貢獻程度也許是不同的(即表現量差異大的可能比較重要)。而GSEA不做任何篩選動作，將所有實驗資料放入分析。
  > >
  > > ……**ORA**的方法……我們關心的是：有興趣的基因中(genes of interest)，與某個基因集(gene set)，共同基因有幾個(*K*值)……我們可以用超幾何分布(Hypergeometric distribution)或二項式分佈(binomial distribution)來計算觀察值*k*的機率。
  > >
  > > ……**GSEA**的概念……首先將高通量實驗所量測到的基因排序，排列的順序是根據實驗量測到的數值決定……GSEA採用一個稱random walk的方法，也就是從基因列表的頭走到尾，如果碰到是基因集的基因就加分，不是則扣分。走完一趟後，回頭看走到哪兒時，分數最高(或最低)，這個分數就是所謂的enrichment score (ES)……GSEA利用permutation testing的方法，也就是隨機抓取同等數量的基因當基因集，並計算得到隨機的ES，去估算實際觀察到的ES的P值，如果P值小於所設定的統計條件，就可以確保這ES並不是隨機就會發生。

`clusterProfiler` 支持多种本体论/通路 (ontology/pathway) 的超几何检验和基因富集分析。并且包内的函数 `enricher()` 和 `GSEA()` 能够分别对用户定义的注释信息进行超几何检验及基因富集分析。

## 4. 输入数据

对于**过表征分析 (over representation analysis, ORA)**, 我们需要的是一个包含基因ID的向量，基因ID可以从差异表达分析获得（例如 `DESeq2` 包）。

对于**基因富集分析 (gene set enrichment analysis, GSEA)**, 我们需要一个经排序的基因列表，在这里我们调用 `DOSE` 包中的示例数据 `geneList` .

> The `geneList` contains three features:
>
> 1. numeric vector: fold change or other type of numerical variable
> 2. named vector: every number was named by the corresponding gene ID
> 3. sorted vector: number should be sorted in decreasing order

```R
data(geneList, package="DOSE")
head(geneList)
# 4312     8318    10874    55143    55388      991 
# 4.572613 4.514594 4.418218 4.144075 3.876258 3.677857 

## 假设想得到 |log2(FC)|>2 的 DEGs.
gene <- names(geneList)[abs(geneList) > 2]
head(gene)
# [1] "4312"  "8318"  "10874" "55143" "55388" "991"
```

## 5. GO分析

### 5.1 支持的物种

GO分析（`groupGO()`, `enrichGO()`, `gseGO()`）支持Bioconductor提供的  `OrgDb` 中已有的20个物种。也可以通过AnnotationHub在线检索并抓取 `OrgDb `.

### 5.2 GO分类

函数 `groupGO()` 的设计是基于在特定水平的GO分布，从而对基因进行分类。

```R
library(org.Hs.eg.db)
## 转换ID，参数'gene'可以是OrgDb支持的任何ID形式
gene.df <- bitr(gene, fromType = "ENTREZID", 
                toType = c("ENSEMBL", "SYMBOL"),
                OrgDb = org.Hs.eg.db) 
head(gene.df)
#   ENTREZID         ENSEMBL SYMBOL
# 1     4312 ENSG00000196611   MMP1
# 2     8318 ENSG00000093009  CDC45
# 3    10874 ENSG00000109255    NMU
# 4    55143 ENSG00000134690  CDCA8
# 5    55388 ENSG00000065328  MCM10
# 6      991 ENSG00000117399  CDC20
```

```R
ggo <- groupGO(gene     = gene,
               OrgDb    = org.Hs.eg.db,
               ont      = "CC", 
               level    = 3,     ## Specific GO Level.
               readable = TRUE)  ## the gene IDs will mapping to gene symbols.
head(ggo)
#                    ID                    Description Count GeneRatio
# GO:0005886 GO:0005886                plasma membrane    52    52/207
# GO:0005628 GO:0005628              prospore membrane     0     0/207
# GO:0005789 GO:0005789 endoplasmic reticulum membrane     8     8/207
# GO:0019867 GO:0019867                 outer membrane     2     2/207
#                                                                                         #                                                                                         #                                                                                         #                                                          geneID
# GO:0005886 S100A9/MELK/S100A8/MARCO/ASPM/CXCL10/LAMP3/CEP55/UGT8/UBE2C/SLC7A5/CXCL9/FADS2/MSLN/IL1R2/KIF18A/S100P/GZMB/TRAT1/GABRP/AQP9/GPR19/SLC2A6/KIF20A/LAG3/NUDT1/CACNA1D/VSTM4/CORIN/KCNK15/CA12/KCNE4/HLA-DQA1/ADH1B/PDZK1/C7/ACKR1/COL17A1/PSD3/EMCN/SLC44A4/LRP2/NLGN4X/MAPT/ERBB4/CX3CR1/LAMP5/ABCA8/STEAP4/PTPRT/TMC5/CYBRD1
# GO:0005628                                                                             #                                                                                         #                                                                                         #                                                                 
# GO:0005789                                                                             #                                                                                         #                                                                                         #               FADS2/CDK1/CHODL/ITPR1/HLA-DQA1/CYP4F8/CYP4B1/FMO5
# GO:0019867                                                                             #                                                                                         #                                                                                         #                                                         MAOB/PGR
```

> Level 1 provides the highest list coverage with the least amount of term specificity. With each increasing level coverage decreases while specificity increases so that level 5 provides the least amount of coverage with the highest term specificity. (Dennis, Glynn, et al)

### 5.3 GO ORA

```R
ego <- enrichGO(gene          = gene,
                universe      = names(geneList),
                OrgDb         = org.Hs.eg.db,
                ont           = "CC",
                pAdjustMethod = "BH",
                pvalueCutoff  = 0.01,
                qvalueCutoff  = 0.05,
        readable      = TRUE)
head(ego)
#                    ID                              Description GeneRatio   BgRatio       pvalue     p.adjust       qvalue
# GO:0000779 GO:0000779 condensed chromosome, centromeric region    14/197  85/11434 1.662431e-10 3.802012e-08 3.572360e-08
# GO:0000776 GO:0000776                              kinetochore    15/197 104/11434 2.551686e-10 3.802012e-08 3.572360e-08
# GO:0000775 GO:0000775           chromosome, centromeric region    17/197 147/11434 5.495085e-10 5.458451e-08 5.128746e-08
# GO:0000793 GO:0000793                     condensed chromosome    17/197 154/11434 1.141189e-09 8.501857e-08 7.988322e-08
#                                                                                         #                  geneID Count
# GO:0000779                   CENPE/NDC80/HJURP/SKA1/NEK2/CENPM/CENPN/ERCC6L/MAD2L1/CDT1/BIRC5/NCAPG/AURKB/CCNB1    14
# GO:0000776              CENPE/NDC80/HJURP/SKA1/NEK2/CENPM/CENPN/ERCC6L/MAD2L1/KIF18A/CDT1/BIRC5/TTK/AURKB/CCNB1    15
# GO:0000775  CDCA8/CENPE/NDC80/HJURP/SKA1/NEK2/CENPM/CENPN/ERCC6L/MAD2L1/KIF18A/CDT1/BIRC5/TTK/NCAPG/AURKB/CCNB1    17
# GO:0000793 CENPE/NDC80/TOP2A/NCAPH/HJURP/SKA1/NEK2/CENPM/CENPN/ERCC6L/MAD2L1/CDT1/BIRC5/NCAPG/AURKB/CHEK1/CCNB1    17
```

如果直接调用 `OrgDb` 中的 ID，则需要在参数中确定 'KeyType'.

```R
ego2 <- enrichGO(gene         = gene.df$ENSEMBL,
                OrgDb         = org.Hs.eg.db,
                keyType       = 'ENSEMBL',
                ont           = "CC",
                pAdjustMethod = "BH",
                pvalueCutoff  = 0.01,
                qvalueCutoff  = 0.05)
```

通过设置参数 `readable=Ture` 或 函数 `setReadable()` 可以将 Gene ID 转换为 symbol.

```R
ego2 <- setReadable(ego2, OrgDb = org.Hs.eg.db)
```

before: 

![](https://upload-images.jianshu.io/upload_images/14383117-bba6cd1c102963f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

after:

![](https://upload-images.jianshu.io/upload_images/14383117-c4bb14e7ac2f30d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5.3.1 移除特定的 GO term 或 GO level

函数 `dropGO` 可以在由 `enrichGO` 得到的结果中移除特定的 GO term 或 GO level.

#### 5.3.2 将结果限定在特定的 Go level

`enrichGO()` 不含设置 GO level 的参数，而函数 `gofilter()` 可以将结果限定在特定的 GO level.

#### 5.3.3 GO term 去冗余

```R
rmredunego <- simplify(ego, cutoff=0.7, by="p.adjust", select_fun=min)
```

before:

![](https://upload-images.jianshu.io/upload_images/14383117-361307cfaad7e93b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

after:

![](https://upload-images.jianshu.io/upload_images/14383117-a024938db9950d90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.4 GO GSEA

```R
ego3 <- gseGO(geneList     = geneList,
              OrgDb        = org.Hs.eg.db,
              ont          = "CC",
              nPerm        = 1000,   ## 排列数
              minGSSize    = 100,
              maxGSSize    = 500,
              pvalueCutoff = 0.05,
              verbose      = FALSE)  ## 不输出结果
```

## 6. KEGG分析

函数 `search_kegg_organism()` 可以帮助搜索 KEGG 数据库支持的物种。

物种缩写戳这里：<https://www.genome.jp/kegg/catalog/org_list.html>

```R
earch_kegg_organism('ece', by='kegg_code')   
#     kegg_code                        scientific_name common_name
# 366       ece Escherichia coli O157:H7 EDL933 (EHEC)        <NA>
ecoli <- search_kegg_organism('Escherichia coli', by='scientific_name')
dim(ecoli)
# [1] 65  3
head(ecoli)
#     kegg_code                        scientific_name common_name
# 361       eco           Escherichia coli K-12 MG1655        <NA>
# 362       ecj            Escherichia coli K-12 W3110        <NA>
# 363       ecd            Escherichia coli K-12 DH10B        <NA>
# 364       ebw                Escherichia coli BW2952        <NA>
# 365      ecok            Escherichia coli K-12 MDS42        <NA>
# 366       ece Escherichia coli O157:H7 EDL933 (EHEC)        <NA>
```

```R
search_kegg_organism('hsa', by='kegg_code')
#   kegg_code scientific_name common_name
# 1       hsa    Homo sapiens       human
dim(hsapiens) 	
[1] 1 3
hsapiens <- search_kegg_organism('Homo sapiens', by='scientific_name')
hsapiens
#   kegg_code scientific_name common_name
# 1       hsa    Homo sapiens       human
```

### 6.1 KEGG ORA

```R
data(geneList, package="DOSE")
gene <- names(geneList)[abs(geneList) > 2]

kk <- enrichKEGG(gene         = gene,
                 organism     = 'hsa',
                 pvalueCutoff = 0.05)
head(kk)
#                ID
# hsa04110 hsa04110
# hsa04114 hsa04114
# hsa04218 hsa04218
# hsa04061 hsa04061
# hsa03320 hsa03320
# hsa04914 hsa04914
#                                                            Description
# hsa04110                                                    Cell cycle
# hsa04114                                                Oocyte meiosis
# hsa04218                                           Cellular senescence
# hsa04061 Viral protein interaction with cytokine and cytokine receptor
# hsa03320                                        PPAR signaling pathway
# hsa04914                       Progesterone-mediated oocyte maturation
#          GeneRatio  BgRatio       pvalue     p.adjust       qvalue
# hsa04110     11/93 124/7861 1.966898e-07 3.815781e-05 3.726753e-05
# hsa04114     10/93 125/7861 1.907932e-06 1.850694e-04 1.807515e-04
# hsa04218     10/93 160/7861 1.742571e-05 1.048593e-03 1.024128e-03
# hsa04061      8/93 100/7861 2.162048e-05 1.048593e-03 1.024128e-03
# hsa03320      7/93  76/7861 2.906892e-05 1.127874e-03 1.101559e-03
# hsa04914      7/93  99/7861 1.587827e-04 5.133975e-03 5.014191e-03
#                                                      geneID Count
# hsa04110 8318/991/9133/890/983/4085/7272/1111/891/4174/9232    11
# hsa04114    991/9133/983/4085/51806/6790/891/9232/3708/5241    10
# hsa04218     2305/4605/9133/890/983/51806/1111/891/776/3708    10
# hsa04061           3627/10563/6373/4283/6362/6355/9547/1524     8
# hsa03320                 4312/9415/9370/5105/2167/3158/5346     7
# hsa04914                    9133/890/983/4085/6790/891/5241     7
```

### 6.2 KEGG GSEA

```R
kk2 <- gseKEGG(geneList     = geneList,
               organism     = 'hsa',
               nPerm        = 1000,
               minGSSize    = 120,
               pvalueCutoff = 0.05,
               verbose      = FALSE)
head(kk2,n=3)
#                ID                Description setSize enrichmentScore
# hsa04151 hsa04151 PI3K-Akt signaling pathway     322      -0.3482755
# hsa04510 hsa04510             Focal adhesion     188      -0.4188582
# hsa03013 hsa03013              RNA transport     131       0.4116488
#                NES      pvalue   p.adjust    qvalues rank
# hsa04151 -1.498250 0.001278772 0.01648352 0.01098901 1997
# hsa04510 -1.712296 0.001390821 0.01648352 0.01098901 2183
# hsa03013  1.750526 0.003067485 0.01648352 0.01098901 3383
#                            leading_edge
# hsa04151 tags=23%, list=16%, signal=20%
# hsa04510 tags=27%, list=17%, signal=23%
# hsa03013 tags=40%, list=27%, signal=29%
#                                                              core_enrichment
# hsa04151 2252/7059/92579/5563/5295/6794/1288/7010/3910/3371/3082/1291/4602/3791/1027/90993/3441/3643/1129/2322/1975/7450/596/3685/1942/2149/1280/4804/3675/595/2261/7248/2246/4803/3912/1902/1278/1277/2846/2057/1293/2247/55970/5618/7058/10161/56034/3693/4254/3480/4908/5159/1292/3908/2690/3909/8817/9223/4915/3551/2791/63923/3913/9863/3667/1287/3679/7060/3479/80310/1311/5105/2066/1101
# hsa04510                                                                                                                         5228/7424/1499/4636/83660/7059/5295/1288/23396/3910/3371/3082/1291/394/3791/7450/596/3685/1280/3675/595/2318/3912/1793/1278/1277/1293/10398/55742/2317/7058/25759/56034/3693/3480/5159/857/1292/3908/3909/63923/3913/1287/3679/7060/3479/10451/80310/1311/1101
# hsa03013                                                                                         10460/1978/55110/54913/9688/8894/11260/10799/9631/4116/5042/8761/6396/23165/8662/10248/55706/79833/9775/29107/23636/5905/9513/5901/10775/10557/4927/79902/1981/26986/11171/10762/8480/8891/11097/26019/10940/4686/9972/81929/10556/3646/9470/387082/1977/57122/8563/7514/79023/3837/9818/56000
```

## 7. 重头戏——功能富集结果的可视化

这时要用到另一个包：`enrichplot` ，它可以实现多种可视化，更好地说明富集分析结果。 

```R
library(enrichplot)
```

### 7.1 Bar plot

```R
oragene <- enrichDGN(gene)
barplot(oragene,showCategory = 20)
## 该函数默认参数为：
## enrichDGN(gene, pvalueCutoff = 0.05, pAdjustMethod = "BH", universe,
##   minGSSize = 10, maxGSSize = 500, qvalueCutoff = 0.2,
##   readable = FALSE)
```

![](https://upload-images.jianshu.io/upload_images/14383117-b8e96f67b5fc9577.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.2 Dot Plot

```R
gseagene <- gseNCG(geneList, nPerm=10000)
p1 <- dotplot(oragene, showCategory=30) + ggtitle("dotplot for ORA")
p2 <- dotplot(gseagene, showCategory=30) + ggtitle("dotplot for GSEA")
plot_grid(p1, p2, ncol=2)
## 一个瘦一个胖好丑啊
```

![](https://upload-images.jianshu.io/upload_images/14383117-133f44e4530abc82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 7.3 Gene-Concept Network

前面的 ~~两款神器~~ 两个函数，都只能展示富集最显著的 GO term，而函数 `cnetplot()` 可以将基因与生物学概念 (e.g.* GO terms or KEGG pathways) 的关系绘制成网状图。

```R
oragnx <- setReadable(oragene, 'org.Hs.eg.db', 'ENTREZID')  ## 将 Gene ID 转换为 symbol
cnetplot(orangx, foldChange=geneList)
```

![](https://upload-images.jianshu.io/upload_images/14383117-0d21e75787ad43c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
cnetplot(oragnx, categorySize="pvalue", foldChange=geneList)  ## categorySize 可以是 "pvalue" 或 "geneNum" 
```
![](https://upload-images.jianshu.io/upload_images/14383117-af6bb77ab68e6343.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```R
cnetplot(oragnx, foldChange=geneList, circular = TRUE, colorEdge = TRUE)  ## 圆形布局，给线条上色
```

![](https://upload-images.jianshu.io/upload_images/14383117-263eca2a0b72cdc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.4 Heatmap

热图能够简化结果，更容易分辨表达模式 (expression patterns) 。

```R
heatplot(oragnx, foldChange=geneList)
```

![](https://upload-images.jianshu.io/upload_images/14383117-6ba29073d27fa3ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.5 Enrichment Map

Enrichment Map 可以将富集条目和重叠的基因集整合为一个网络图，相互重叠的基因集则趋向于成簇，从而易于分辨功能模型。

```R
emapplot(oragene)
```

![](https://upload-images.jianshu.io/upload_images/14383117-680833c5ff61572a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.6 UpSet Plot

函数 `upsetplot()` 是 `cneplot()` 的一种替代方案，用于可视化基因与基因集间的复杂关联，而 `upsetplot()` 更着重于不同基因集间基因的重叠情况。

```R
upsetplot(oragene)
```



![](https://upload-images.jianshu.io/upload_images/14383117-4e149a3ac32b21bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.7 GSEA结果的表达分布叠嶂图 (ridgeline plot for expression distribution of GSEA result)

更直观地展示上调/下调的通路。

```R
ridgeplot(gseagene)
```



![](https://upload-images.jianshu.io/upload_images/14383117-3393ea3ed35f3693.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.8 富集条目在 PubMed 上的趋势

函数 `pmcplot()` 可以就某些富集 条目/通路 绘制其在 PubMed 上的 **数量/比例** 折线图。

```R
terms <- oragene$Description[1:3]
p <- pmcplot(terms, 2012:2019) ## 默认为proportion=TRUE
p2 <- pmcplot(terms, 2012:2019, proportion=FALSE)
plot_grid(p, p2, ncol=2)
```

![](https://upload-images.jianshu.io/upload_images/14383117-ed6d360bce0a0e33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.9 goplot

函数 `goplot()` 接受 `enrichGO()` 的输出，并将其可视化。

![](https://upload-images.jianshu.io/upload_images/14383117-5b6f6a862af9db7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 7.10 browseKEGG

函数 `browseKEGG` 可以帮你打开浏览器，嗯。

```R
browseKEGG(kk, 'hsa04110')
```

![](https://upload-images.jianshu.io/upload_images/14383117-8d4ff5001ce12b58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.11 `pathview` 包里的 ~~上帝视角~~ PATHVIEW!

```R
library(pathview）
hsa04110 <- pathview(gene.data  = geneList,
                     pathway.id = "hsa04110",
                     species    = "hsa",
                     limit      = list(gene=max(abs(geneList)), cpd=1)) ## cpd, compound
# Info: Downloading xml files for hsa04110, 1/1 pathways..
# Info: Downloading png files for hsa04110, 1/1 pathways..
# 'select()' returned 1:1 mapping between keys and columns
# Info: Working in directory /YOUR PATH/Project/clusterProfiler
# Info: Writing image file hsa04110.pathview.png
```

![](https://upload-images.jianshu.io/upload_images/14383117-7fe2d9d004199652.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## References

- **clusterProfiler Vignette**: <https://yulab-smu.github.io/clusterProfiler-book/>

- **醫學研究部(Department of Medical Research) 共同研究室電子報**：如何剖析高通量數據Gene-Set Approach <https://ntuhmc.ntuh.gov.tw/epaper-54th.htm> 

- Dennis G, Sherman B T, Hosack D A, et al. **DAVID: database for annotation, visualization, and integrated discovery**[J]. Genome biology, 2003, 4(9): R60.

- **GUANGCHUANG YU**: use simplify to remove redundancy of enriched GO terms <https://guangchuangyu.github.io/2015/10/use-simplify-to-remove-redundancy-of-enriched-go-terms/>
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
