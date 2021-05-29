> ![](https://upload-images.jianshu.io/upload_images/14383117-bcb81513d1a7a0a0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## affy: Methods for Affymetrix Oligonucleotide Arrays

> Gautier L, Cope L, Bolstad BM, Irizarry RA (2004). “affy—analysis of Affymetrix GeneChip data at the probe level.” *Bioinformatics*, **20**(3), 307–315. ISSN 1367-4803, doi: [10.1093/bioinformatics/btg405](https://doi.org/10.1093/bioinformatics/btg405).

## oligo: Preprocessing tools for oligonucleotide arrays

> Carvalho BS, Irizarry RA (2010). “A Framework for Oligonucleotide Microarray Preprocessing.” *Bioinformatics*, **26**(19), 2363-7. ISSN 1367-4803, doi: [10.1093/bioinformatics/btq431](https://doi.org/10.1093/bioinformatics/btq431).

## 1. 关于这两个包

Jimmy大神是这么说的：

> affy包是R语言的bioconductor系列包的一个，就一个功能，读取affymetix的基因表达芯片数据-CEL格式数据，处理成表达矩阵！！！
>
> oligo包是R语言的bioconductor系列包的一个，就一个功能，读取affymetix的基因表达芯片数据-CEL格式数据，处理成表达矩阵！！！

一些术语：

> **probe** oligonucleotides of 25 base pair length used to probe RNA targets.
> **perfect match** probes intended to match perfectly the target sequence.
> ***PM*** intensity value read from the perfect matches.
> **mismatch** the probes having one base mismatch with the target sequence intended to account for non-specific binding.
> ***MM*** intensity value read from the mis-matches.
> **probe pair** a unit composed of a perfect match and its mismatch.
> **affyID** an identification for a probe set (which can be a gene or a fraction of a gene) represented on the array.
> **probe pair set** PMs and MMs related to a common affyID
>
> **CEL files** contain measured intensities and locations for an array that has been hybridized.
> **CDF file** contain the information relating probe pair sets to locations on the array.

那么就来**读取affymetix的基因表达芯片数据-CEL格式数据，处理成表达矩阵！！！**

## 2. `affy`

### 2.1 读取 *CEL* 文件

文件来自 [GSE1428][ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE1nnn/GSE1428/suppl/GSE1428_RAW.tar]

两种读取方法：

- 在 `celfile.path =` 设置 *CEL* 文件所在路径

  ```R
  library(affy)
  dir_cels <- "YOUR PATH/affy/GSE1428_RAW"
  affyData <- ReadAffy(celfile.path = dir_cels)
  ```

- 通过 `tkWidgets` 包弹出窗口

  ```R
  library(affy)
  library(tkWidgets)
  affyData <- ReadAffy(widget=TRUE)
  ```
![](https://upload-images.jianshu.io/upload_images/14383117-42b9033379d5593d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  这时候直接鼠标点击选择即可...(*￣０￣)ノ

### 2.2 Expression measures 

将探针水平数据转换为表达量值需要以下几步：

> 1. reading in probe level data. 
>
> 2. background correction. 
>
> 3. normalization. 
>
> 4. probe speci c background correction, e.g. subtracting *MM*. 
>
> 5. summarizing the probe set values into one expression measure and, in some cases, a standard error for this summary. 

`affy` 有一系列函数可以完成以上任务。

#### 2.2.1 expresso

`expresso` 的参数选择：

```r 
bgcorrect.methods()
# [1] "bg.correct" "mas"        "none"       "rma" 
normalize.methods(affyData)
# [1] "constant"         "contrasts"        "invariantset"    
# [4] "loess"            "methods"          "qspline"         
# [7] "quantiles"        "quantiles.robust"
pmcorrect.methods() 
# [1] "mas"        "methods"    "pmonly"     "subtractmm"
express.summary.stat.methods() 
# [1] "avgdiff"      "liwong"       "mas"          "medianpolish"
# [5] "playerout"   
```

同样的，也可以通过 `tkWidgets` 包弹出窗口进行选择👇

```R
expresso(affyData, widget=TRUE) 
```

![](https://upload-images.jianshu.io/upload_images/14383117-4ab8f54d826044e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择好参数后，`expresso` 一顿操作得到一个 `ExpressionSet` 对象。

```r
eset <- expresso(affyData, 
                 bgcorrect.method="rma",
                 normalize.method="qspline", 
                 pmcorrect.method="pmonly", 
                 summary.method="liwong")
# background correction: rma 
# normalization: qspline 
# PM/MM correction : pmonly 
#  expression values: liwong 
# background correcting...
# done.
# normalizing...[1] "samples= 496 k= 5 first= 999"
# ...
# done.
# 22283 ids to be processed
# |                    |
# |####################|
class(eset)
# [1] "ExpressionSet"
# attr(,"package")
# [1] "Biobase"
```

然后就得到了**表达矩阵**！！！

```R
expmtx_expso <- exprs(eset)
## 也可以选择输出为txt
write.exprs(eset, file="mydata.txt") 
```

![](https://upload-images.jianshu.io/upload_images/14383117-245a3869290875a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.2.2 MAS 5.0

如果想用 MAS 5.0 算法，使用 `mas5()` 即可。

> **MAS 5.0** is the name of the Affymetrix algorithm used for producing gene expression signal (see [Affymetrix WebSite](http://www.affymetrix.com/)) for more details on the algorithm.
>
> The algorithm consists of background correction, calculation of the probe summary and scaling (typically termed probeset-level normalization). 

所以就是这样的：

```R
eset_mas5 <- mas5(affyData)
# background correction: mas 
# PM/MM correction : mas 
# expression values: mas 
# background correcting...done.
# 22283 ids to be processed
# |                    |
# |####################|
```

#### 2.2.3 RMA

对应的，RMA 算法有 `rma()` 函数。

> **RMA** is an algorithm used to create an expression matrix from Affymetrix data. The raw intensity values are background corrected, log2 transformed and then quantile normalized. Next a linear model is fit to the normalized data to obtain an expression measure for each probe set on each array.

```R
eset_rma <- rma(affyData)
# Background correcting
# Normalizing
# Calculating Expression
```

### 2.3 数据挖掘中的质量控制

查看 `ExpressionSet` 对象的相关信息。

```R
affyData
# AffyBatch object
# size of arrays=712x712 features (27 kb)
# cdf=HG-U133A (22283 affyids)
# number of samples=22
# number of genes=22283
# annotation=hgu133a
# notes=
phenoData(affyData)
# An object of class 'AnnotatedDataFrame'
#   sampleNames: GSM23735.CEL GSM23736.CEL ... GSM23757.CEL
#     (22 total)
#   varLabels: sample
#   varMetadata: labelDescription
pData(affyData)
# sample
# GSM23735.CEL      1
# GSM23736.CEL      2
# GSM23737.CEL      3
# GSM23738.CEL      4
# GSM23740.CEL      5
# GSM23741.CEL      6
# GSM23742.CEL      7
# GSM23743.CEL      8
# GSM23744.CEL      9
# GSM23745.CEL     10
# GSM23746.CEL     11
# GSM23747.CEL     12
# GSM23748.CEL     13
# GSM23749.CEL     14
# GSM23750.CEL     15
# GSM23751.CEL     16
# GSM23752.CEL     17
# GSM23753.CEL     18
# GSM23754.CEL     19
# GSM23755.CEL     20
# GSM23756.CEL     21
# GSM23757.CEL     22
```

函数 `MAplot()` 可将强度数据的成对对比 (pairwise graphical) 以图像形式呈现出来。

```
MAplot(affyData[1:4],pairs=TRUE,plot.method="smoothScatter")
```

![](https://upload-images.jianshu.io/upload_images/14383117-3c149e797a811ff3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.3.1 查看 PM MM 数据

分别利用函数 `pm()` 及 `mm()` :

```R
affypm <- pm(affyData, gn[50])
```

![](https://upload-images.jianshu.io/upload_images/14383117-39d0d272a46b5a1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
affymm <- mm(affyData, gn[50])
```

![](https://upload-images.jianshu.io/upload_images/14383117-11beaab905d49ed2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一个快速得到 “大于 PM 的 MM 的百分比” 的方法：

```R
mean(mm(affyData)>pm(affyData))
# [1] 0.3590052
```

## 3. `oligo`

### 3.1 读取数据

`oligo` 中，`list.celfiles()` 用于生成 CEL 文件列表，`read.celfiles()` 用于读取 CEL 文件。

```R
celFiles <- list.celfiles('YOUR PATH/GSE1428_RAW',
                          full.names=TRUE) 
rawData <- read.celfiles(celFiles) 
```

`oligo` 具有多种类型的 data container:

| Type                | Array      |
| ------------------- | ---------- |
| *ExonFeatureSet*    | Exon ST    |
| *ExpressionFeature* | Expression |
| *GeneFeatureSet*    | Gene ST    |
| *TilingFeatureSet*  | Tiling     |
| *SnpFeatureSet*     | SNP        |

### 3.2 处理成表达矩阵

`rma()` 利用 RMA 算法进行背景校正、归一化及 summarization. 得到 `ExpressionSet` 对象。

```R
exset_rma <- rma(rawData)
# Background correcting
# Normalizing
# Calculating Expression
class(exset_rma)
# [1] "ExpressionSet"
# attr(,"package")
# [1] "Biobase"
```

然后就得到了**表达矩阵**！！！

```R
expmatx <- exprs(exset_rma)
```

![](https://upload-images.jianshu.io/upload_images/14383117-f7a740bf313a78d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然也可以分步进行：

```r
backgroundCorrectionMethods() 
# [1] "rma"  "mas"  "LESN"
bgdata <- backgroundCorrect(rawData, method = 'mas' ) ## 实际上默认就是'mas'
normdata <- normalize(bgdata)
# Normalizing... OK
```

### 3.3 PM 可视化

```R
boxplot(rawData, value = "pm")
```

![](https://upload-images.jianshu.io/upload_images/14383117-6e5d1bde90de9102.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
boxplot(exset_rma, value = "pm")
  ```

![](https://upload-images.jianshu.io/upload_images/14383117-eaec37d42d662ec3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同样的，参数 `value = ` 'mm', 'bg', 'both', 'all' , 获得对应的 boxplot.

### 3.4 探针序列

```R
pmseq <- pmSequence(rawData)
pmseq
#   A DNAStringSet instance of length 247965
#          width seq
#      [1]    25 TCGTTTTGGACTGACCTGGGGGTAT
#      [2]    25 CGAATTGTACGTGGGTCTGGACTTA
#      [3]    25 TGGACTAAACGAGATTTTAACCAGT
#      [4]    25 CCGACAGGGGTAGACGTTCCACTTG
#      [5]    25 AGGATTTAAGTCTGAGGTAGTCAAC
#      ...   ... ...
# [247961]    25 TTGTGAAGCACACAGCTCTGCAGCC
# [247962]    25 TTGGTGAGCACACTCCATTGACCAC
# [247963]    25 TTGGGAGAAGTGCCATTTCAGCAGA
# [247964]    25 TTGGGAAAGCATGGCACCCTCAGAC
# [247965]    25 TTGTGGACAGTGAGCACCGTCGTCA
```

得到 affinity splines coefficients, 画 Sequence effect 图：

```R
pmsLog2 <- log2(pm(rawData)) 
coefs <- getAffinitySplineCoefficients(pmsLog2, pmseq) 
getBaseProfile(coefs[,1], plot = TRUE)
```

![](https://upload-images.jianshu.io/upload_images/14383117-33aa658e44e9a911.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1, 2, 3, 4可能分别对应 A, C, G, T.

为什么说可能呢，因为示例里的图是这样的...

![](https://upload-images.jianshu.io/upload_images/14383117-7e337c0cba7de2fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

后来找到了老版的说明书，原来是要再加些参数...完美解决👇

```R
colors <- darkColors(4)
xL <- "Base Position"
yL <- "Effect"
pchs <- c("A", "C", "G", "T")
getBaseProfile(coefs[,1], plot = TRUE, 
               pch = pchs, type = "b", 
               xlab = xL, ylab = yL,
               lwd = 3, col = colors)
```

![](https://upload-images.jianshu.io/upload_images/14383117-6aead02df5369d55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## References

- 用affy包读取affymetix的基因表达芯片数据-CEL格式数据 <http://www.bio-info-trainee.com/1580.html>

- 用oligo包来读取affymetix的基因表达芯片数据-CEL格式数据 http://www.bio-info-trainee.com/1586.html

- MAS 5.0 - GENE User Manual 1.3 http://gene.moffitt.org/libaffy/doc/multi/MAS-5_002e0.html

- Robust Multi-array Average (RMA) http://www.molmine.com/magma/loading/rma.htm
- oligo User's Guide https://bioconductor.org/packages/release/bioc/vignettes/oligo/inst/doc/oug.pdf
- oligo - Primer https://www.bioconductor.org/packages//2.10/bioc/vignettes/oligo/inst/doc/primer.pdf

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
