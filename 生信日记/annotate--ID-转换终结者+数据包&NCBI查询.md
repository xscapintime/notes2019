> ![](https://upload-images.jianshu.io/upload_images/14383117-afd790c069324ec5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Annotation for microarrays

> Gentleman R (2019). *annotate: Annotation for microarrays*. R package version 1.62.0.

## 1. 关于 `annotate`

作者是 R. Gentleman, 不知道是不是这位真の大佬👇 （似乎是的，大佬的姓氏一看就是大佬👀
![](https://upload-images.jianshu.io/upload_images/14383117-0a3e510124648436.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

包内函数主要分为四类：

- 可从特定元数据库 (meta-data libraries) 提取数据的接口函数 (Interface function)

- 支持查询 NLM 和 NCBI 网页
- 支持查询染色体坐标数据并能和 `geneplotter` 包协作作图

- 可将基因列表输出为 HTML 文件，并能超链接连接到不同的网页资源

~~简单~~ 并不简单地分别试跑一下前三项的示例代码，再尝试一下 Boss Jimmy 博客提到过的 “ID转换终结者” ┗|｀O′|┛ 、

## 2. 从芯片元数据库 (meta-data libraries) 提取数据

以 Affymetrix HGu95av2 芯片为例，比如想得到 GO 相关数据。

```R
affyGO <- eapply(hgu95av2GO, getOntology)
## 查看各探针对应的 GO ID 数目
table(sapply(affyGO, length)) 
# 
#    0    1    2    3    4    5    6    7    8    9   10   11   12 
# 1901 1577 1866 1768 1440 1136  744  575  367  322  239  154  137 
#   13   14   15   16   17   18   19   20   21   22   23   24   25 
#   89   57   68   27   30   19   17   14   11   11    7    5    2 
#   26   27   28   29   30   32   37 
#   16    3    3    8    1    6    5 
```

查看各探针对应的 GO evidence codes 及数目：

```R
affyEv <- eapply(hgu95av2GO, getEvidence)
table(unlist(affyEv, use.names=FALSE))
# 
#  EXP   HDA   HEP   HMP    IC   IDA   IEA   IEP   IGI   IMP   IPI 
#  457  6758    80   114  1302 59621 65824  1073  1759 19887 14319 
#  ISA   ISM   ISS   NAS    ND   RCA   TAS 
#  979   743 23946  7448   675   146 43804 
```

> A [GO annotation](http://geneontology.org/docs/go-annotations/) is a statement about the function of a particular gene. Each annotation includes an evidence code to indicate how the annotation to a particular term is supported.
>
> Evidence codes fall into six general categories:
>
> - experimental evidence
> - phylogenetic evidence
> - computational evidence
> - author statements
> - curatorial statements
> - automatically generated annotations

删除特定的 GO evidence codes: 

```R
affyEv_drop <- eapply(hgu95av2GO, dropECode, c("IEA", "ND"))
table(unlist(sapply(affyEv_drop, getEvidence),
             use.names = FALSE))
# 
#   EXP   HDA   HEP   HMP    IC   IDA   IEP   IGI   IMP   IPI 
#   457  6758    80   114  1302 59621  1073  1759 19887 14319 
#   ISA   ISM   ISS   NAS   RCA   TAS 
#   979   743 23946  7448   146 43804 
```

## 3. 在线查询信息

需要和 `Biobase` 和 `XML` 这两个包配合使用。

### 3.1 四个直截了当的函数

- `genbank`

- `pubmed`

- `entrezGeneByID`

- `entrezGeneQuery`

`genbank` 和 `pubmed` 可通过参数选择返回 XML 格式数据或打开浏览器，`entrezGeneByID` 和 `entrezGeneQuery` 则可返回 URL 链接。

```R
library(annotate)
entrezGeneQuery(c("leukemia", "Homo sapiens"))
# [1] "https://www.ncbi.nlm.nih.gov//sites/entrez?db=gene&cmd=search&term=leukemia%20Homo sapiens"
```
![](https://upload-images.jianshu.io/upload_images/14383117-2db8164f1069da88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
## emmmmm这样是会报错的
entrezGeneQuery("leukemia", "Homo sapiens") 
# Error in entrezGeneQuery("leukemia", "Homo sapiens") : 
#   unused argument ("Homo sapiens")
```

当直接输入 UniGene ID 时， `entrezGeneByID` 和 `entrezGeneQuery` 可以说是等同的：

```R
entrezGeneByID(c("100", "1002"))
# [1] "https://www.ncbi.nlm.nih.gov//sites/entrez?db=gene&cmd=search&term=100" 
# [2] "https://www.ncbi.nlm.nih.gov//sites/entrez?db=gene&cmd=search&term=1002"
entrezGeneQuery(100)
# [1] "https://www.ncbi.nlm.nih.gov//sites/entrez?db=gene&cmd=search&term=100"
entrezGeneByID(100)
# [1] "https://www.ncbi.nlm.nih.gov//sites/entrez?db=gene&cmd=search&term=100"
```

### 3.2 获取 PubMed 信息

要用到 `Biobase` 包内的数据：`sample.ExpressionSet` .

```R
data(sample.ExpressionSet) 
## 取其中11个基因作为示例
affys <- featureNames(sample.ExpressionSet)[490:500] 
affys
# [1] "31729_at" "31730_at" "31731_at" "31732_at" "31733_at" "31734_at"
# [7] "31735_at" "31736_at" "31737_at" "31738_at" "31739_at"
```

这时需要把 Affymetrix id (identifiers) 转换成 PubMed ID，以便接下来 `pubmed` 的愉快操作。

```R
library(hgu95av2.db) 
ids <- getPMID(affys,"hgu95av2") 
```

得到了一个有11个元素的 list，接下来对它进行 unlist.

```R
ids <- unlist(ids,use.names=FALSE) 
## 去重
ids <- unique(ids[!is.na(as.numeric(ids))]) 
length(ids) 
# [1] 731
ids[1:10]
# [1] "11438666" "12477932" "12878157" "15489334" "16710414" "17375202"
# [7] "17643375" "17884155" "18029348" "19240132"
```

`pubmed` 返回了 XMLDocument 对象，即用11个 Affymetrix id 得到了731个 PMID, 再取前10个获取其他信息。

```R
x <- pubmed(ids[1:10]) 
class(x)
[1] "XMLDocument"         "XMLAbstractDocument"
a <- xmlRoot(x) 
numAbst <- length(xmlChildren(a)) 
numAbst 
# [1] 10
```

构建 `PubMedAbst` 对象，提取摘要文本：

```R
arts <- vector("list", length=numAbst) 
absts <- rep(NA, numAbst) 
for (i in 1:numAbst) {
  arts[[i]] <- buildPubMedAbst(a[[i]])
  absts[i] <- abstText(arts[[i]])
}
```

```R
arts[[3]] 
# An object of class 'pubMedAbst':
# Title: Redifferentiation of dedifferentiated
#      chondrocytes and chondrogenesis of human
#      bone marrow stromal cells via chondrosphere
#      formation with expression profiling by
#      large-scale cDNA analysis.
# PMID: 12878157
# Authors: H Imabayashi, T Mori, S Gojo, T Kiyono,
#      T Sugiyama, R Irie, T Isogai, J Hata, Y
#      Toyama, A Umezawa
# Journal: Exp Cell Res
# Date: Aug 2003
```

```R
absts[3]
# [1] "Characterization of dedifferentiated chondrocytes (DECs) and mesenchymal stem cells capable of differentiating into chondrocytes is of biological and clinical interest. We isolated DECs and bone marrow stromal cells (BMSCs), H4-1 and H3-4, and demonstrated that the cells started to produce extracellular matrices, such as type II collagen and aggrecan, at an early stage of chondrosphere formation. Furthermore, cDNA sequencing of cDNA libraries constricted by the oligocapping method was performed to analyze difference in mRNA expression profiling between DECs and marrow stromal cells. Upon redifferentiation of DECs, cartilage-related extracellular matrix genes, such as those encoding leucine-rich small proteoglycans, cartilage oligomeric matrix protein, and chitinase 3-like 1 (cartilage glycoprotein-39), were highly expressed. Growth factors such as FGF7 and CTGF were detected at a high frequency in the growth stage of monolayer stromal cultures. By combining the expression profile and flow cytometry, we demonstrated that isolated stromal cells, defined by CD34(-), c-kit(-), and CD140alpha(- or low), have chondrogenic potential. The newly established human mesenchymal cells with expression profiling provide a powerful model for a study of chondrogenic differentiation and further understanding of cartilage regeneration in the means of redifferentiated DECs and BMSCs."
```

## 4. 染色体信息的构建及利用

利用函数 `buildChromLocation()` 构建 `chromLocation` 对象：

```R
z <- buildChromLocation("hgu95av2") 
z
# Instance of a chromLocation class with the following fields:
# 	Organism:  Homo sapiens 
# 	Data source:  hgu95av2 
# 	Number of chromosomes for this organism:  595 
# 	Chromosomes of this organism and their lengths in base pairs:
# 		 1 : 248956422
# 		 2 : 242193529
# 		 3 : 198295559
# 		 4 : 190214555
# 		 5 : 181538259
# 		 6 : 170805979
# 		 7 : 159345973
# 		 X : 156040895
# 		 8 : 145138636
# 		 9 : 138394717
# 		 11 : 135086622
# 		 10 : 133797422
# 		 12 : 133275309
# 		 13 : 114364328
# 		 14 : 107043718
# 		 15 : 101991189
# 		 16 : 90338345
# 		 17 : 83257441
# 		 18 : 80373285
# 		 20 : 64444167
# 		 19 : 58617616
# 		 Y : 57227415
# 		 22 : 50818468
# 		 21 : 46709983
# 		 8_KZ208915v1_fix : 6367528
# 		 15_KI270905v1_alt : 5161414
# 		 15_KN538374v1_fix : 4998962
# 		 6_GL000256v2_alt : 4929269
# 		 6_GL000254v2_alt : 4827813
# 		 6_GL000251v2_alt : 4795265
# 		 6_GL000253v2_alt : 4677643
# 		 6_GL000250v2_alt : 4672374
# 		 6_GL000255v2_alt : 4606388
# 		 6_GL000252v2_alt : 4604811
# 		 17_KI270857v1_alt : 2877074
# 		 16_KI270853v1_alt : 2659700
# ...
## 后面太长不贴出来了
```

这个 S4 对象有6个 slot.

![](https://upload-images.jianshu.io/upload_images/14383117-c99d974f457f8b6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


相关信息获取：

```R
## 物种信息
organism(z)
# [1] "Homo sapiens"

## 数据来源
dataSource(z)
# [1] "hgu95av2"

## 查看染色体位点
head(names(chromLocs(z)), n = 10) 
#  [1] "1"  "10" "11" "12" "13" "14" "15" "16" "17" "18"
tail(names(chromLocs(z)), n = 10) 
#  [1] "19_KV575249v1_alt" "19_KV575251v1_alt"
#  [3] "19_KV575252v1_alt" "19_KV575253v1_alt"
#  [5] "19_KV575254v1_alt" "19_KV575255v1_alt"
#  [7] "19_KV575256v1_alt" "19_KV575257v1_alt"
#  [9] "19_KV575259v1_alt" "19_KV575260v1_alt"

## 定位探针所在染色体
get("32972_at", probesToChrom(z))
# [1] "X"

## 查看染色体名称及其长度
head(chromInfo(z))
#         1         2         3         4         5 
# 248956422 242193529 198295559 190214555 181538259 
#         6 
# 170805979 
tail(chromInfo(z))
# Un_KI270312v1 Un_KI270539v1 Un_KI270385v1 Un_KI270423v1 
#           998           993           990           981 
# Un_KI270392v1 Un_KI270394v1 
#           971           970 

## 查看探针所对应的 gene symbol
get("32972_at", geneSymbols(z))
# [1] "NOX1"

## 染色体数
nChrom(z)
# [1] 595
```

## 5. ID 转换终结者

### 5.1 `getSymbol()`

以 `getSymbol()` 为例的一系列函数：

> getSYMBOL(x, data)
> getEG(x, data)
> getGO(x, data)
> getPMID(x, data)
> getGOdesc(x, which)
> lookUp(x, data, what, load = FALSE)

```R
probes <- featureNames(sample.ExpressionSet)[246:260]
```

```R
getSYMBOL(probes, "hgu95av2.db")
#   31490_at 31491_s_at   31492_at 31493_s_at   31494_at 
#    "SCN5A"    "CASP8"    "EIF3K"         NA         NA 
#   31495_at 31496_g_at   31497_at 31498_f_at 31499_s_at 
#     "XCL2"         NA         NA         NA   "FCGR3B" 
```

```R
getEG(probes, "hgu95av2.db")
#   31490_at 31491_s_at   31492_at 31493_s_at   31494_at 
#     "6331"      "841"    "27335"         NA         NA 
#   31495_at 31496_g_at   31497_at 31498_f_at 31499_s_at 
#     "6846"         NA         NA         NA     "2215" 
```

```R
go <- getGO(probes, "hgu95av2.db")
```

![](https://upload-images.jianshu.io/upload_images/14383117-cbc35db240c3d285.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
getGOdesc(go[[1]][[1]][["GOID"]], "ANY")
# $`GO:0002027`
# GOID: GO:0002027
# Term: regulation of heart rate
# Ontology: BP
# Definition: Any process that modulates the frequency
#     or rate of heart contraction.
# Synonym: cardiac chronotropy
# Synonym: regulation of heart contraction rate
# Synonym: regulation of rate of heart contraction
getGOdesc(go[[1]][[1]][["GOID"]], "BP")
# $`GO:0002027`
# GOID: GO:0002027
# Term: regulation of heart rate
# Ontology: BP
# Definition: Any process that modulates the frequency
#     or rate of heart contraction.
# Synonym: cardiac chronotropy
# Synonym: regulation of heart contraction rate
# Synonym: regulation of rate of heart contraction

## 试错
getGOdesc(go[[1]][[1]][["GOID"]], "MF")
# NULL
```

```R
lookUp(probes, "hgu95av2", "ENTREZID")
# $`31490_at`
# [1] "6331"
# 
# $`31491_s_at`
# [1] "841"
# 
# $`31492_at`
# [1] "27335"
# 
# $`31493_s_at`
# [1] NA
# 
# $`31494_at`
# [1] NA
# 
# $`31495_at`
# [1] "6846"
# 
# $`31496_g_at`
# [1] NA
# 
# $`31497_at`
# [1] NA
# 
# $`31498_f_at`
# [1] NA
# 
# $`31499_s_at`
# [1] "2215"

lookUp(go[[2]][[1]][["GOID"]], "GO", "ONTOLOGY")
# 'select()' returned 1:1 mapping between keys and columns
# $`GO:0006508`
# [1] "BP"
```
### 5.2 `select()`

先用 `colums()` 查看可以转换的项目：

```R
columns(hgu95av2.db)
#  [1] "ACCNUM"       "ALIAS"        "ENSEMBL"     
#  [4] "ENSEMBLPROT"  "ENSEMBLTRANS" "ENTREZID"    
#  [7] "ENZYME"       "EVIDENCE"     "EVIDENCEALL" 
# [10] "GENENAME"     "GO"           "GOALL"       
# [13] "IPI"          "MAP"          "OMIM"        
# [16] "ONTOLOGY"     "ONTOLOGYALL"  "PATH"        
# [19] "PFAM"         "PMID"         "PROBEID"     
# [22] "PROSITE"      "REFSEQ"       "SYMBOL"      
# [25] "UCSCKG"       "UNIGENE"      "UNIPROT"     
```

```R
out <- select(hgu95av2.db, probes,  c("SYMBOL","ENTREZID", "GENENAME"))
```
![](https://upload-images.jianshu.io/upload_images/14383117-da3997692e48b7d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## References

- **Annotation Overview** https://bioconductor.org/packages/release/bioc/vignettes/annotate/inst/doc/annotate.pdf
- **Basic GO Usage** https://bioconductor.org/packages/release/bioc/vignettes/annotate/inst/doc/GOusage.pdf
- **Guide to GO evidence codes** <http://geneontology.org/docs/guide-go-evidence-codes/>
- **HOWTO: Use the online query tools** https://bioconductor.org/packages/release/bioc/vignettes/annotate/inst/doc/query.pdf
- **HowTo: use chromosomal information** https://bioconductor.org/packages/release/bioc/vignettes/annotate/inst/doc/chromLoc.pdf
- **Question: Converting Affymetrix Probes To Gene Ids**, Diwan <https://www.biostars.org/p/76097/>

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)



