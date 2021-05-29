>![](https://upload-images.jianshu.io/upload_images/14383117-994bf33b043622b7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Gene set enrichment analysis with topGO 

> Alexa A, Rahnenfuhrer J (2019). *topGO: Enrichment Analysis for Gene Ontology*. R package version 2.36.0.
>
> DOI: [10.18129/B9.bioc.topGO](https://doi.org/doi:10.18129/B9.bioc.topGO)  

## 1. 关于 `topGO` & 安装三连

`topGO` 的设计旨在实现 GO term 的半自动富集分析。一个主要优点是它提供的统一化的基因集检测方案 (the unified gene set testing framework) 。接下来是对这个主要优点的展开，Besides, also 句型——轻松地应用函数完成GO富集分析、容易地执行新的统计学检测或新算法、对比不同的GO富集研究方法。

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("topGO")
```

```R
library(topGO)
```

```R
ls("package:topGO")
# [1] "algorithm"           "algorithm<-"        
# [3] "allGenes"            "allMembers"         
# [5] "allMembers<-"        "allParents"         
# [7] "allScore"            "annFUN"             
# [9] "annFUN.db"           "annFUN.file"        
# [11] "annFUN.gene2GO"      "annFUN.GO2genes"    
# [13] "annFUN.org"          "attrInTerm"         
# [15] "buildLevels"         "combineResults"     
# [17] "contTable"           "countGenesInTerm"   
# [19] "cutOff"              "cutOff<-"           
# [21] "depth"               "depth<-"            
# [23] "description"         "description<-"      
# [25] "elim"                "elim<-"             
# [27] "expressionMatrix"    "feasible"           
# [29] "feasible<-"          "geneData"           
# [31] "geneData<-"          "genes"              
# [33] "geneScore"           "geneSelectionFun"   
# [35] "geneSelectionFun<-"  "genesInTerm"        
# [37] "GenTable"            "getGraphRoot"       
# [39] "getNoOfLevels"       "getPvalues"         
# [41] "getSigGroups"        "getSigRatio"        
# [43] "GOBPTerm"            "GOCCTerm"           
# [45] "GOFisherTest"        "GOglobalTest"       
# [47] "GOKSTest"            "GOKSTiesTest"       
# [49] "GOMFTerm"            "GOplot"             
# [51] "GOSumTest"           "GOtTest"            
# [53] "graph"               "graph<-"            
# [55] "groupGOTerms"        "inducedGraph"       
# [57] "initialize"          "inverseList"        
# [59] "joinFun"             "members"            
# [61] "members<-"           "membersExpr"        
# [63] "membersScore"        "Name"               
# [65] "Name<-"              "nodesInInducedGraph"
# [67] "numAllMembers"       "numGenes"           
# [69] "numMembers"          "numSigAll"          
# [71] "numSigGenes"         "numSigMembers"      
# [73] "ontology"            "ontology<-"         
# [75] "penalise"            "permSumStats"       
# [77] "permSumStats.all"    "phenotype"          
# [79] "print"               "printGenes"         
# [81] "printGraph"          "pType"              
# [83] "pType<-"             "rankMembers"        
# [85] "readMappings"        "reverseArch"        
# [87] "runTest"             "score"              
# [89] "score<-"             "scoreOrder"         
# [91] "scoresInTerm"        "show"               
# [93] "showGroupDensity"    "showSigOfNodes"     
# [95] "sigAllMembers"       "sigGenes"           
# [97] "sigMembers"          "sigMembers<-"       
# [99] "sigRatio<-"          "termStat"           
# [101] "testName"            "testName<-"         
# [103] "testStatistic"       "testStatPar"        
# [105] "updateGenes"         "updateGroup"        
# [107] "updateTerm<-"        "usedGO"             
# [109] "Weights"             "Weights<-"          
# [111] "whichAlgorithms"     "whichTests"   
```

## 2. 数据准备

调用 `ALL` 包的内置数据。

```R
library(ALL)
data("ALL")
data(geneList) ## geneList, a list of genes and the respective p-values for differential expression
affyLib <- paste(annotation(ALL), "db", sep = ".") 
library(package = affyLib, character.only = TRUE)
# 载入需要的程辑包：org.Hs.eg.db
## 载入多个包时，加上 character.only = TRUE 参数，可以确保正确载入
```

函数 `topDiffGenes()` 可以在 p-values=0.01 的显著水平上筛选 `geneList` 中的差异表达基因。

```R
sum(topDiffGenes(geneList)) 
# [1] 50
```

万事俱备，开始 **构建 `topGOdata`** ，这个对象包含全部的基因指标 (gene identifiers) 和其数值、GO注释、GO 层次结构 (GO hierarchical structure) 以及其他用于富集分析的信息。

```R
sampleGOdata <- new("topGOdata",
                    description = "Simple session",
                    ontology = "BP",
                    allGenes = geneList,
                    geneSel = topDiffGenes, ## 设定筛选函数
                    nodeSize = 10, ## 删去少于10个注释基因的GO term
                    annot = annFUN.db, ## maps genes identifiers to GO terms from the affyLib object
                    affyLib = affyLib)
sampleGOdata
# 
# ------------------------- topGOdata object -------------------------
# 
#  Description:
#    -  Simple session 
# 
#  Ontology:
#    -  BP 
# 
#  323 available genes (all genes from the array):
#    - symbol:  1095_s_at 1130_at 1196_at 1329_s_at 1340_s_at  ...
#    - score :  1 1 0.62238 0.541224 1  ...
#    - 50  significant genes. 
# 
#  309 feasible genes (genes that can be used in the analysis):
#    - symbol:  1095_s_at 1130_at 1196_at 1329_s_at 1340_s_at  ...
#    - score :  1 1 0.62238 0.541224 1  ...
#    - 45  significant genes. 
# 
#  GO graph (nodes with at least  10  genes):
#    - a graph with directed edges
#    - number of nodes = 1072 
#    - number of edges = 2319 
# 
# ------------------------- topGOdata object -------------------------
```

## 3. 富集检测

- 用到的两种统计学方法：Fisher's exact test, Kolmogorov-Smirnov like test.
- 用到的函数：`runTest()`，可将特定算法和T检验方法应用于输入的对象，并返回一个 `topGOresult` 对象。

### 3.1 经典 over-representation GO富集分析

```R
resultFisher <- runTest(sampleGOdata, algorithm = "classic", statistic = "fisher") 
# 
# 			 -- Classic Algorithm -- 
# 
# 		 the algorithm is scoring 964 nontrivial nodes
# 		 parameters: 
# 			 test statistic: fisher
resultFisher
# 
# Description: Simple session 
# Ontology: BP 
# 'classic' algorithm with the 'fisher' test
# 1072 GO terms scored: 45 terms with p < 0.01
# Annotation data:
#     Annotated genes: 309 
#     Significant genes: 45 
#     Min. no. of genes annotated to a GO: 10 
#     Nontrivial nodes: 964 
class(resultFisher)
# [1] "topGOresult"
# attr(,"package")
# [1] "topGO"
```

### 3.2 利用 Kolmogorov-Smirnov like test 的富集分析

两种方法：*classic, elim*

> **'elim' :** this method processes the GO terms by traversing the GO hierarchy from bottom to top.
>
> **'classic'**: each GO term is tested independently, not taking the GO hierarchy into account.

```R
resultKS.classic <- runTest(sampleGOdata, algorithm = "classic", statistic = "ks") 
# 
# 			 -- Classic Algorithm -- 
# 
# 		 the algorithm is scoring 1072 nontrivial nodes
# 		 parameters: 
# 			 test statistic: ks
# 			 score order: increasing
resultKS.classic
# 
# Description: Simple session 
# Ontology: BP 
# 'classic' algorithm with the 'ks' test
# 1072 GO terms scored: 83 terms with p < 0.01
# Annotation data:
#     Annotated genes: 309 
#     Significant genes: 45 
#     Min. no. of genes annotated to a GO: 10 
#     Nontrivial nodes: 1072 
```

```R
resultKS.elim <- runTest(sampleGOdata, algorithm = "elim", statistic = "ks")
# 
# 			 -- Elim Algorithm -- 
# 
# 		 the algorithm is scoring 1072 nontrivial nodes
# 		 parameters: 
# 			 test statistic: ks
# 			 cutOff: 0.01
# 			 score order: increasing
resultKS.elim
# 
# Description: Simple session 
# Ontology: BP 
# 'elim' algorithm with the 'ks : 0.01' test
# 1072 GO terms scored: 24 terms with p < 0.01
# Annotation data:
#     Annotated genes: 309 
#     Significant genes: 45 
#     Min. no. of genes annotated to a GO: 10 
#     Nontrivial nodes: 1072 
```

### ps. 查看所有允许的算法和T检验方法

```R
whichAlgorithms() 
# [1] "classic"     "elim"        "weight"      "weight01"   
# [5] "lea"         "parentchild"
whichTests() 
# [1] "fisher"     "ks"         "t"          "globaltest"
# [5] "sum"        "ks.ties"   
```

## 4. 结果分析

### 4.1 得到富集显著 GO term 的 data frame

函数 `GenTable()` 可用于分析富集最为显著的 GO term 和相对应的p值。

```R
allRes <- GenTable(sampleGOdata,classicFisher = resultFisher,
                   classicKS = resultKS.classic,
                   elimKS = resultKS.elim,
                   orderBy = "elimKS",ranksOf = "classicFisher",
                   topNodes = 10)
```

![](https://upload-images.jianshu.io/upload_images/14383117-eb9ed1c706895eb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.2 不同算法所得p值的对比及可视化

函数 `score()` 可以得到 `topGOresult` 对象中 GO term 的p值。

```R
pValue.classic <- score(resultKS.classic) 
pValue.elim <- score(resultKS.elim)[names(pValue.classic)] 
```

```R
gstat <- termStat(sampleGOdata, names(pValue.classic))  ## to summarise the statistics
```

![](https://upload-images.jianshu.io/upload_images/14383117-fc4785e632c57c46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
colMap <- function(x) {
  .col <- rep(rev(heat.colors(length(unique(x)))), time = table(x))
  return(.col[match(1:length(x), order(x))])
}
gCol <- colMap(gstat$Significant)
```

```R
plot(pValue.classic, pValue.elim, 
     xlab = "p-value classic", 
     ylab = "p-value elim", 
     pch = 19, cex = gSize, col = gCol)

plot(log10(pValue.classic), log10(pValue.elim), 
     xlab = "lg(p-value) classic", 
     ylab = "lg(p-value) elim", 
     pch = 19, cex = gSize, col = gCol)
```

![](https://upload-images.jianshu.io/upload_images/14383117-09024d15238eebea.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出两种方法得到的p值确实是有差异的，所以显然存在一些被A方法认定显著——而在B方法下不显著的 GO term. 可以通过下面的方法找到它们：

```R
sel.go <- names(pValue.classic)[pValue.elim < pValue.classic] 
cbind(termStat(sampleGOdata, sel.go), 
      elim = pValue.elim[sel.go],
      classic = pValue.classic[sel.go])
#            Annotated Significant Expected       elim    classic
# GO:0006807       224          36    32.62 0.44604086 0.53079040
# GO:0019538       164          23    23.88 0.37042531 0.47406305
# GO:0043170       206          35    30.00 0.05327402 0.06576399
# GO:1901564       189          26    27.52 0.73230230 0.90604207
```

### 4.3 GO Graph

方形为显著性前五的 GO term.

```R
showSigOfNodes(sampleGOdata, score(resultKS.elim), 
               firstSigNodes = 5, useInfo = 'all')
```

![](https://upload-images.jianshu.io/upload_images/14383117-4c568425db353510.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5. 其他的可视化技能

### 5.1 p值的直方图

```R
hist(pValue.classic, 50, xlab = "p-values_KSclassic")
```

![](https://upload-images.jianshu.io/upload_images/14383117-cd95f1e232545726.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.2 分析单个GO term

函数 `showGroupDensity()` 可以解释**显著富集到某个GO term的基因**是否比其他基因有更高的分值。

```R
goID <- allRes[1, "GO.ID"]
showGroupDensity(sampleGOdata, goID, ranks = TRUE)
```

![](https://upload-images.jianshu.io/upload_images/14383117-a87564e655bc6623.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## References

- **Gene set enrichment analysis with topGO**

  <https://bioconductor.org/packages/release/bioc/vignettes/topGO/inst/doc/topGO.pdf>

- **avrilomics** Using TopGO to test for GO-term enrichment 

  http://avrilomics.blogspot.com/2015/07/using-topgo-to-test-for-go-term.html

- **Quick-R** <https://www.statmethods.net/advgraphs/parameters.html>

- **davetgerrard** LiverProteins/scriptx/topGO_vignetteTrial.R

  <https://github.com/davetgerrard/LiverProteins/blob/master/scripts/topGO_vignetteTrial.R>

##### 悄咪咪吐槽一下作者

![](https://upload-images.jianshu.io/upload_images/14383117-d29bbfef692fea1c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)


