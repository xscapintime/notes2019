> 今天是找不到meme的我
![](https://upload-images.jianshu.io/upload_images/14383117-c2732a3b76faea0c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 高通量测序数据 Cufflinks 的可视化及挖掘

> Goff L, Trapnell C, Kelley D (2019). cummeRbund: Analysis, exploration, manipulation, and visualization of Cufflinks high-throughput sequencing data.. R package version 2.26.0.
>
> DOI: [10.18129/B9.bioc.cummeRbund](https://doi.org/doi:10.18129/B9.bioc.cummeRbund)

## 1. 关于 `cummeRbund` 
cummerbund 本意是“腹带”，“转录组tuxedo” = tophat+cufflinks+cummeRbund.
![Source: //sourceforge.net/projects/trinityrnaseq/](https://upload-images.jianshu.io/upload_images/14383117-affd4f0b41ddebfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


`cummeRbund` 的设计，旨在帮助运用操作 Cufflinks RNA-Seq 差异表达分析后产生的大量数据。

`cummebund` 可识别两种类型数据：'pointer' 和 'data' , 每种数据都能够与 **FPKM 值**、 **差异表达信息**、**统计学检测结果**、**原始数据及归一化后的数据 (raw and normalized fragment counts)**、**独立/个体/自身(?)重复 (individual replicate) FPKM 值**、**注释信息**直接联系。输出文件以标准 R object 形式 (data.frame. list, etc) 存在，允许浏览和分析。

作为分析和可视化工具，`cummeRbund` 为 RNA-Seq 数据质量及其全局统计、简单的表达量绘图提供了大量工具。

## 2. cuffdiff 结果读取

```R
library(cummeRbund)

## 读取包内的示例数据
cuff <- readCufflinks("YOUR PATH/R/win-library/3.6/cummeRbund/extdata") 
cuff
# CuffSet instance with:
# 	 3 samples
# 	 400 genes
# 	 1203 isoforms
# 	 662 TSS
# 	 906 CDS
# 	 1062 promoters
# 	 1986 splicing
#	 990 relCDS
```

## 3. 全局统计及质量控制 (Global statistics and Quality Control)

### 3.1 评估模型拟合质量

鉴于 RNA-Seq 数据通常都有过度离散的问题。函数 `dispersionPlot()` 可以将模型拟合情况可视化。

```R
dispersionPlot(genes(cuff))
```

![](https://upload-images.jianshu.io/upload_images/14383117-f6c19be4a3f49718.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.2 判断基因层面及mRNA异构体 (isoform) 层面的数据质量

平方离散系数 (squared coefficient of variation, SCV) 是一种重复间变异性 (cross-replicate) 的归一化度量， 能够实现其在不同条件下的可视化。

```R
fpkmSCVPlot(genes(cuff))
fpkmSCVPlot(isoforms(cuff))
```

![](https://upload-images.jianshu.io/upload_images/14383117-ee7ff7f2ff3bf7bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.3 评估样本间FPKM值的分布

```R
csDensity(genes(cuff))
csDensity(genes(cuff),replicates=T)
## replicates=TRUE 即展示重复间的FPKM分布
```

![](https://upload-images.jianshu.io/upload_images/14383117-d9cad185e6e30246.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以 box plot 形式呈现：

```R
csBoxplot(genes(cuff))
csBoxplot(genes(cuff),replicates=T)
```

![](https://upload-images.jianshu.io/upload_images/14383117-736ac9dcda438437.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.4 鉴定成对比对条件下基因表达的全局变化

```R
csScatterMatrix(genes(cuff))
```

![](https://upload-images.jianshu.io/upload_images/14383117-91e6ccd04128c42f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单独得到其中的一张：

```R
csScatter(genes(cuff),"hESC","Fibroblasts",smooth=T
```

![](https://upload-images.jianshu.io/upload_images/14383117-56496a8606cd5682.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.5 聚类分析

```R
csDendro(genes(cuff))
csDendro(genes(cuff),replicates = T)
## replicates=TRUE 可以鉴定异常/离群重复
```

![](https://upload-images.jianshu.io/upload_images/14383117-c3668f4fea7f60d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.6 判断不同条件间可能的系统性偏差

函数 `MAplot()` 可以用于分析成对比对的 强度 vs 倍数变化 ( intensity vs fold-change) 关系。

```R
MAplot(genes(cuff),"hESC","Fibroblasts") 
MAplot(genes(cuff),"hESC","Fibroblasts",useCount=T) 
## useCount=T, 用归一化的count值代替FPKM
```

![](https://upload-images.jianshu.io/upload_images/14383117-ace100e0a22697bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3.7 火山图

```R
csVolcanoMatrix(genes(cuff))
csVolcano(genes(cuff),"hESC","Fibroblasts")
```

![](https://upload-images.jianshu.io/upload_images/14383117-657ce5d5953fdf37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/14383117-6a102bb7cea8a8c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. 获取数据

### 4.1 Cuffdiff 运行数据

```R
runInfo(cuff)
```

![](https://upload-images.jianshu.io/upload_images/14383117-6eee537b38c63e34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
replicates(cuff) 
#            file sample_name replicate      rep_name
# 1  iPS_rep1.bam         iPS         0         iPS_0
# 2  iPS_rep2.bam         iPS         1         iPS_1
# 3   H1_rep1.bam        hESC         0        hESC_0
# 4   H1_rep3.bam        hESC         1        hESC_1
# 5 NHLF_rep1.bam Fibroblasts         0 Fibroblasts_0
# 6 NHLF_rep2.bam Fibroblasts         1 Fibroblasts_1
#   total_mass norm_mass internal_scale external_scale
# 1     173431    706934       0.958068       0.584877
# 2     173007    706934       1.037970       0.584877
# 3     754749    706934       0.989851       1.513060
# 4     762643    706934       1.010250       1.513060
# 5     876775    706934       0.840416       1.223240
# 6    1412130    706934       1.198470       1.223240
```

### 4.2 注释信息 (Features/Annotation)

#### 4.2.1 `annotation()`

```R
gene.features<-annotation(genes(cuff)) 
head(gene.features)
#       gene_id class_code nearest_ref_id gene_short_name
# 1 XLOC_000001       <NA>           <NA>            <NA>
# 2 XLOC_000002       <NA>           <NA>           OR4F5
# 3 XLOC_000003       <NA>           <NA>            <NA>
# 4 XLOC_000004       <NA>           <NA>            <NA>
# 5 XLOC_000005       <NA>           <NA>            <NA>
# 6 XLOC_000006       <NA>           <NA>          OR4F16
#                locus length coverage feature_id isoform_id
# 1   chr1:11873-29961     NA       NA         NA       <NA>
# 2   chr1:69090-70008     NA       NA         NA       <NA>
# 3 chr1:321083-321114     NA       NA         NA       <NA>
# 4 chr1:321145-321223     NA       NA         NA       <NA>
# 5 chr1:322036-328580     NA       NA         NA       <NA>
# 6 chr1:367658-368595     NA       NA         NA       <NA>
#   seqnames source type start end score strand frame
# 1     <NA>   <NA>   NA    NA  NA    NA   <NA>  <NA>
# 2     <NA>   <NA>   NA    NA  NA    NA   <NA>  <NA>
# 3     <NA>   <NA>   NA    NA  NA    NA   <NA>  <NA>
# 4     <NA>   <NA>   NA    NA  NA    NA   <NA>  <NA>
# 5     <NA>   <NA>   NA    NA  NA    NA   <NA>  <NA>
# 6     <NA>   <NA>   NA    NA  NA    NA   <NA>  <NA>
```

#### 4.2.2 `fpkm()`

```R
gene.fpkm<-fpkm(genes(cuff)) 
head(gene.fpkm)
#       gene_id sample_name       fpkm    conf_hi conf_lo
# 1 XLOC_000001 Fibroblasts    16.1506   182.9240 0.00000
# 2 XLOC_000002 Fibroblasts     0.0000     0.0000 0.00000
# 3 XLOC_000003 Fibroblasts     0.0000     0.0000 0.00000
# 4 XLOC_000004 Fibroblasts 14237.7000 78180.9000 0.00000
# 5 XLOC_000005 Fibroblasts    48.0566    90.6526 5.46055
# 6 XLOC_000006 Fibroblasts     0.0000     0.0000 0.00000
#   quant_status      stdev
# 1           OK    83.3867
# 2           OK     0.0000
# 3           OK     0.0000
# 4           OK 31971.6000
# 5           OK    21.2980
# 6           OK     0.0000
```
```R
isoform.fpkm<-fpkm(isoforms(cuff)) 
head(isoform.fpkm)
#       isoform_id sample_name        fpkm   conf_hi conf_lo
# 1 TCONS_00000001 Fibroblasts    10.99630   118.178       0
# 2 TCONS_00000002 Fibroblasts     0.00000     0.000       0
# 3 TCONS_00000003 Fibroblasts     5.15434   132.926       0
# 4 TCONS_00000004 Fibroblasts     0.00000     0.000       0
# 5 TCONS_00000005 Fibroblasts     0.00000     0.000       0
# 6 TCONS_00000006 Fibroblasts 14237.70000 78180.900       0
#   quant_status       stdev
# 1           OK    53.59085
# 2           OK     0.00000
# 3           OK    63.88583
# 4           OK     0.00000
# 5           OK     0.00000
# 6           OK 31971.60000
```

#### 4.2.3 `repFpkm()`

```R
gene.repFpkm<-repFpkm(genes(cuff))
head(gene.repFpkm)
#       gene_id sample_name replicate      rep_name  raw_frags
# 1 XLOC_000001 Fibroblasts         0 Fibroblasts_0  12.000100
# 2 XLOC_000002 Fibroblasts         0 Fibroblasts_0   0.000000
# 3 XLOC_000003 Fibroblasts         0 Fibroblasts_0   0.000000
# 4 XLOC_000004 Fibroblasts         0 Fibroblasts_0   0.333333
# 5 XLOC_000005 Fibroblasts         0 Fibroblasts_0 137.100000
# 6 XLOC_000006 Fibroblasts         0 Fibroblasts_0   0.000000
#   internal_scaled_frags external_scaled_frags       fpkm
# 1             14.278800             11.672900    11.1815
# 2              0.000000              0.000000     0.0000
# 3              0.000000              0.000000     0.0000
# 4              0.396629              0.324245 28475.5000
# 5            163.134000            133.362000    57.8929
# 6              0.000000              0.000000     0.0000
#   effective_length status
# 1               NA     OK
# 2               NA     OK
# 3               NA     OK
# 4               NA     OK
# 5               NA     OK
# 6               NA     OK
```

#### 4.2.4 `count()`

```R
gene.counts<-count(genes(cuff))
head(gene.counts)      
#       gene_id sample_name      count    variance uncertainty
# 1 XLOC_000001 Fibroblasts  20.072500 1.05160e+04 1.77636e-15
# 2 XLOC_000002 Fibroblasts   0.000000 0.00000e+00 0.00000e+00
# 3 XLOC_000003 Fibroblasts   0.000000 0.00000e+00 0.00000e+00
# 4 XLOC_000004 Fibroblasts   0.198315 1.98315e-01 0.00000e+00
# 5 XLOC_000005 Fibroblasts 131.575000 3.35882e+03 5.68434e-14
# 6 XLOC_000006 Fibroblasts   0.000000 0.00000e+00 0.00000e+00
#    dispersion status
# 1 1311.870000     OK
# 2    0.000000     OK
# 3    0.000000     OK
# 4    0.198315     OK
# 5 2161.890000     OK
# 6    0.000000     OK
```

#### 4.2.5 `diffData()`

```R
gene.diff<-diffData(genes(cuff)) 
head(gene.diff)
#       gene_id sample_1 sample_2 status   value_1     value_2
# 1 XLOC_000001      iPS     hESC NOTEST  20.21750 3.47386e-01
# 2 XLOC_000002      iPS     hESC NOTEST   0.00000 0.00000e+00
# 3 XLOC_000003      iPS     hESC NOTEST   0.00000 0.00000e+00
# 4 XLOC_000004      iPS     hESC     OK   0.00000 6.97259e+05
# 5 XLOC_000005      iPS     hESC     OK 355.82300 6.96704e+02
# 6 XLOC_000006      iPS     hESC NOTEST   1.51396 0.00000e+00
#   log2_fold_change     test_stat    p_value    q_value
# 1     -5.86292e+00   7.13525e-01 0.47552100 1.00000000
# 2      0.00000e+00   0.00000e+00 1.00000000 1.00000000
# 3      0.00000e+00   0.00000e+00 1.00000000 1.00000000
# 4     1.79769e+308  1.79769e+308 0.00857693 0.02109120
# 5      9.69385e-01  -2.98373e+00 0.00284757 0.00840284
# 6    -1.79769e+308 -1.79769e+308 0.24382400 1.00000000
#   significant
# 1          no
# 2          no
# 3          no
# 4         yes
# 5         yes
# 6          no
```

### 4.3 样本名称及 feature 名称

```R
sample.names<-samples(genes(cuff)) 
sample.names
# [1] "iPS"         "hESC"        "Fibroblasts"
gene.featurenames<-featureNames(genes(cuff)) 
head(gene.featurenames)
# [1] "XLOC_000001" "XLOC_000002" "XLOC_000003" "XLOC_000004"
# [5] "XLOC_000005" "XLOC_000006"
```

### 4.4 几个实用函数

#### 4.4.1 `fpkmMatrix()`

```R
gene.matrix<-fpkmMatrix(genes(cuff))
head(gene.matrix)
#                   iPS        hESC Fibroblasts
# XLOC_000001  20.21750 3.47386e-01     16.1506
# XLOC_000002   0.00000 0.00000e+00      0.0000
# XLOC_000003   0.00000 0.00000e+00      0.0000
# XLOC_000004   0.00000 6.97259e+05  14237.7000
# XLOC_000005 355.82300 6.96704e+02     48.0566
# XLOC_000006   1.51396 0.00000e+00      0.0000
```

#### 4.4.2 `repFpkmMatrix()`

```R
gene.rep.matrix<-repFpkmMatrix(genes(cuff)) 
head(gene.rep.matrix)
#                iPS_0     iPS_1      hESC_0      hESC_1
# XLOC_000001  17.2049  22.92880       0.000 6.21918e-01
# XLOC_000002   0.0000   0.00000       0.000 0.00000e+00
# XLOC_000003   0.0000   0.00000       0.000 0.00000e+00
# XLOC_000004   0.0000   0.00000 1377990.000 8.35811e+05
# XLOC_000005 319.0300 390.45500     687.563 7.21983e+02
# XLOC_000006   0.0000   3.02791       0.000 0.00000e+00
#             Fibroblasts_0 Fibroblasts_1
# XLOC_000001       11.1815       20.6009
# XLOC_000002        0.0000        0.0000
# XLOC_000003        0.0000        0.0000
# XLOC_000004    28475.5000        0.0000
# XLOC_000005       57.8929       38.0084
# XLOC_000006        0.0000        0.0000
```

#### 4.4.3 `countMatrix()`

```R
gene.count.matrix<-countMatrix(genes(cuff)) 
head(gene.count.matrix)
#                    iPS        hESC Fibroblasts
# XLOC_000001  11.440900    0.494996   20.072500
# XLOC_000002   0.000000    0.000000    0.000000
# XLOC_000003   0.000000    0.000000    0.000000
# XLOC_000004   0.000000    5.680560    0.198315
# XLOC_000005 486.456000 2495.560000  131.575000
# XLOC_000006   0.481711    0.000000    0.000000
```

## 5. 创建 Gene Sets

### 5.1 筛选基因

```R
data(sampleData)
myGeneIds<-sampleIDs
myGeneIds
# [1] "XLOC_001363" "XLOC_001297" "XLOC_001339" "XLOC_000132"
# [5] "XLOC_001265" "XLOC_000151" "XLOC_001359" "XLOC_000069"
# [9] "XLOC_000170" "XLOC_000105" "XLOC_001262" "XLOC_001348"
# [13] "XLOC_001411" "XLOC_001369" "XLOC_000158" "XLOC_001370"
# [17] "XLOC_001263" "XLOC_000115" "XLOC_000089" "XLOC_001240"
```

```R
myGenes<-getGenes(cuff,myGeneIds) 
myGenes
# CuffGeneSet instance for  20  genes
#  
# Slots:
# 	 annotation
# 	 fpkm
# 	 repFpkm
# 	 diff
# 	 count
# 	 isoforms	 CuffFeatureSet instance of size 45 
# 	 TSS		 CuffFeatureSet instance of size 23 
# 	 CDS		 CuffFeatureSet instance of size 36 
# 	 promoters		 CuffFeatureSet instance of size 20 
# 	 splicing		 CuffFeatureSet instance of size 23 
# 	 relCDS		 CuffFeatureSet instance of size 20 
```

### 5.2 Gene set层面的可视化

#### 5.2.1 `csHeatmap()`

函数 `csHeatmap()` 可以以 CuffGeneSet 或 CuffFeatureSet 对象作为输入，绘制出热图。

```R
csHeatmap(myGenes,cluster='both') 
csHeatmap(myGenes,cluster='both',replicates=T)
```

![](https://upload-images.jianshu.io/upload_images/14383117-1a00a804b381c8f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5.2.2 `expressionBarplot()`

```R
expressionBarplot(myGenes) 
```

![](https://upload-images.jianshu.io/upload_images/14383117-a8ccbfa95d96ff4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5.2.3 `csScatter()`

函数 `csScatter()` 用于绘制任意两种条件下的比对散点图。

```R
csScatter(myGenes,"Fibroblasts","hESC",smooth=T)  
```

![](https://upload-images.jianshu.io/upload_images/14383117-9c6867770dfee669.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 5.2.4 `csVolcano()`

```R
csVolcano(myGenes,"Fibroblasts","hESC") 
```

![](https://upload-images.jianshu.io/upload_images/14383117-6c05ffdb0df05620.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 6. 单个基因 (Individual Genes)

 ### 6.1 获得 gene object

```R
myGeneId<-"PINK1"
myGene<-getGene(cuff,myGeneId) 
myGene 
# CuffGene instance for gene XLOC_000172 
# Short name:	 PINK1 
# Slots:
# 	 annotation
# 	 features
# 	 fpkm
# 	 repFpkm
# 	 diff
# 	 count
# 	 isoforms	 CuffFeature instance of size 2 
# 	 TSS		 CuffFeature instance of size 2 
# 	 CDS		 CuffFeature instance of size 2 
```

### 6.2 基因层面的可视化

#### 6.2.1 `expressionPlot()`

```R
expressionPlot(myGene)
expressionPlot(myGene,replicates=TRUE) 
```

![](https://upload-images.jianshu.io/upload_images/14383117-d705e83538931b8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
expressionPlot(isoforms(myGene),replicates=T) 
```

![](https://upload-images.jianshu.io/upload_images/14383117-caa57e752af4274e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
expressionPlot(CDS(myGene),replicates=T) 
```

![](https://upload-images.jianshu.io/upload_images/14383117-a2a928777150f4c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 6.2.2 `expressionBarplot()`

```R
expressionBarplot(myGene) 
expressionBarplot(myGene,replicates=T)
```

![](https://upload-images.jianshu.io/upload_images/14383117-7aba8b4e2f22ecd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 6.2.3 `csPie()`

```R
csPie(myGene,level="isoforms")
```

![](https://upload-images.jianshu.io/upload_images/14383117-4be1e598cb5b025a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 7. 差异表达分析

### 7.1 鉴定差异表达基因

```R
sigGeneIds<-getSig(cuff,alpha=0.05,level="genes")
head(sigGeneIds)
# [1] "XLOC_000004" "XLOC_000005" "XLOC_000008"
# [4] "XLOC_000009" "XLOC_000011" "XLOC_000013"
length(sigGeneIds) 
# [1] 207
```

#### 7.1.1 创建差异基因的 gene sets

```R
sigGenes<-getGenes(cuff,sigGeneIds) 
sigGenes
# CuffGeneSet instance for  207  genes
#  
# Slots:
# 	 annotation
# 	 fpkm
# 	 repFpkm
# 	 diff
# 	 count
# 	 isoforms	 CuffFeatureSet instance of size 717 
# 	 TSS		 CuffFeatureSet instance of size 399 
# 	 CDS		 CuffFeatureSet instance of size 577 
# 	 promoters		 CuffFeatureSet instance of size 207 
# 	 splicing		 CuffFeatureSet instance of size 399 
# 	 relCDS		 CuffFeatureSet instance of size 207 
```

#### 7.1.2 可视化

```R
csVolcano(sigGenes,"Fibroblasts","hESC") 
```

![](https://upload-images.jianshu.io/upload_images/14383117-1d58fff1f47c578f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
不用 `csHeatmap()` 或 `expressionBarplot()` 画全局，是因为最后得到的基因太多了 (:з)∠)_...前面的筛选参数只有 corrected q-values < 0.5，还需要其他的筛选步骤才能得到真正的DEG.

## 8. 寻找相似基因

函数 `fndSimilar()` 可以鉴定出与给定基因最相似的基因，并允许设置相似基因的数量。

```R
mySimilar<-findSimilar(cuff,"PINK1",n=20)
expressionPlot(mySimilar,logMode=T,showErrorbars=F)
```

![](https://upload-images.jianshu.io/upload_images/14383117-6001c779e0cccaeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## References
- **CummeRbund User Guide**: https://bioconductor.org/packages/release/bioc/vignettes/cummeRbund/inst/doc/cummeRbund-manual.pdf
- **Sample cummeRbund workflow**: https://bioconductor.org/packages/release/bioc/vignettes/cummeRbund/inst/doc/cummeRbund-example-workflow.pdf
---

# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
