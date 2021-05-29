> *ce n'est que le premier pas qui coûte.*
![](https://upload-images.jianshu.io/upload_images/14383117-4424f3d90b0a5412.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# seqinr 

## 生物序列获取和分析

> Charif D, Lobry J, Necsulea A, et al. The seqinr Package[J]. R Packag, 2007.

## 1. 关于 `seqinr`

字面意思上，`seqinr` 只是 "Sequences in **R**" 的缩写，也可以理解为 “从GenBank等数据库获取 (**R**etrieve) 序列”，很好地解释了 `seqinr` 的主要功能：在 R 中，提供一种高效的访问序列数据库的方法。

## 2. 以人类CDS序列为例实战一波

### 2.1 从 Ensembl 下载 FASTA 文件

![](https://upload-images.jianshu.io/upload_images/14383117-809a224d14a0aacd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2 读入文件

```R
library(seqinr)
human_cds <- read.fasta("Homo_sapiens.GRCh38.cds.all.fa")
```

![](https://upload-images.jianshu.io/upload_images/14383117-addf6b762888aec6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个转录本都是这个 list 的一个元素。

```R
human_cds[["ENST00000390572.1"]]
#  [1] "a" "g" "c" "a" "t" "a" "t" "t" "g" "t" "g" "g" "t" "g" "g" "t" "g" "a" "c" "t"
# [21] "g" "c" "t" "a" "t" "t" "c" "c"
# attr(,"name")
# [1] "ENST00000390572.1"
# attr(,"Annot")
# [1] ">ENST00000390572.1 cds chromosome:GRCh38:14:105888551:105888578:-1 gene:ENSG00000211912.1 gene_biotype:IG_D_gene transcript_biotype:IG_D_gene gene_symbol:IGHD2-21 description:immunoglobulin heavy diversity 2-21 [Source:HGNC Symbol;Acc:HGNC:5491]"
# attr(,"class")
# [1] "SeqFastadna"
```

### 2.3 几个统计序列概况的函数

- `count()` 

  统计序列中寡聚物 (oligomers) 的数量，参数 `wordsize` 决定 "n-mer".

- `GC()`  

  统计核苷酸序列中的GC含量。

- `length()` 

```R
cds66 <- human_cds[[66]]
cds66
#  [1] "t" "g" "a" "c" "t" "a" "c" "a" "g" "t" "a" "a" "c" "t" "a"
# [16] "c"
# attr(,"name")
# [1] "ENST00000634085.1"
# attr(,"Annot")
# [1] ">ENST00000634085.1 cds chromosome:GRCh38:CHR_HSCHR14_3_CTG1:105913993:105914008:-1 gene:ENSG00000282227.1 gene_biotype:IG_D_gene transcript_biotype:IG_D_gene gene_symbol:IGHD4-4 description:immunoglobulin heavy diversity 4-4 [Source:HGNC Symbol;Acc:HGNC:5505]"
# attr(,"class")
# [1] "SeqFastadna"
```

```R
count(cds66,1)
# 
# a c g t 
# 6 4 2 4 
count(cds66,2)
# 
# aa ac ag at ca cc cg ct ga gc gg gt ta tc tg tt 
#  1  4  1  0  1  0  0  2  1  0  0  1  3  0  1  0 
count(cds66,3)
# 
# aaa aac aag aat aca acc acg act aga agc agg agt ata atc atg att 
#   0   1   0   0   1   0   0   2   0   0   0   1   0   0   0   0 
# caa cac cag cat cca ccc ccg cct cga cgc cgg cgt cta ctc ctg ctt 
#   0   0   1   0   0   0   0   0   0   0   0   0   2   0   0   0 
# gaa gac gag gat gca gcc gcg gct gga ggc ggg ggt gta gtc gtg gtt 
#   0   1   0   0   0   0   0   0   0   0   0   0   1   0   0   0 
# taa tac tag tat tca tcc tcg tct tga tgc tgg tgt tta ttc ttg ttt 
#   1   2   0   0   0   0   0   0   1   0   0   0   0   0   0   0 
```

```R
GC(cds66)
# [1] 0.375
```

```R
length(cds66)
# [1] 16
```

### 2.4 提取注释信息

```R
annotation <- getAnnot(human_cds)
annotation[[66]]
# [1] ">ENST00000634085.1 cds chromosome:GRCh38:CHR_HSCHR14_3_CTG1:105913993:105914008:-1 gene:ENSG00000282227.1 gene_biotype:IG_D_gene transcript_biotype:IG_D_gene gene_symbol:IGHD4-4 description:immunoglobulin heavy diversity 4-4 [Source:HGNC Symbol;Acc:HGNC:5505]"
```

发现只有 "description" 里的信息比较有辨识度，于是——

```R
cancer_index <- grep("cancer",annotation,ignore.case = T)
length(cancer_index)
# [1] 95
cancer_index
#  [1]   1891   1892   5120   5121   5122  16950  16951  16952  16953  16954
# [11]  16955  16956  16957  23827  23828  23829  23830  23831  29035  29036
# [21]  37044  37132  37207  37219  37239  37259  37405  37484  37503  37537
# [31]  47785  56845  57929  59658  59659  59714  59715  60447  60448  68942
# [41]  78490  78491  78492  78493  78494  78495  78496  78497  78498  84049
# [51]  84050  84051  84052  86921  86922  86923  86924  86925  86926  86927
# [61]  88828  88834  88882  88883  92802  92803  97079  97080  97081  97082
# [71]  97083  97084  97085  97086 100900 101862 101863 102719 102720 102723
# [81] 102724 102879 102880 102890 102891 102901 102902 102973 102974 102976
# [91] 102977 102978 102979 102988 102989
```

验证一下，"description" 里确实是有 "cancer" 的。

```R
annotation[[102989]]
# [1] ">ENST00000594117.4 cds chromosome:GRCh38:X:135718930:135723539:1 gene:ENSG00000268940.5 gene_biotype:protein_coding transcript_biotype:protein_coding gene_symbol:CT45A1 description:cancer/testis antigen family 45 member A1 [Source:HGNC Symbol;Acc:HGNC:33267]"
```

### 2.5 将筛选结果导出为 FASTA 文件

```R
cancer_transcript <- human_cds[cancer_index]
length(cancer_transcript)
# [1] 95   ## 和index的长度是一样的
cancer_annotation <- getAnnot(cancer_transcript)
cancer_sequence <- getSequence(cancer_transcript)
write.fasta(cancer_sequence,cancer_annotation,"human_cancertscpt.fa")
```

再导入文件查看一下：

```R
human_cancertscpt <- read.fasta("human_cancertscpt.fa")
```

![](https://upload-images.jianshu.io/upload_images/14383117-749e80e592323909.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.  一些可视化操作

### 3.1 序列长度分布图

以上面得到的 `cancer_transcript` 为例。

```R
getLength(cancer_transcript)
#  [1]  795  729 2142  468 1245 2103 2454 2520 1926  729 2334 2475 1674 1821 1473
# [16] 1890  267   99  213  210  867  867  867  867  867  867  867  867  867  867
# [31]  411  411  342  543  633  543  507  633  543  975 2169 1647 2073 2151 2031
# [46]  108 1974  138  278 2142 1707 1578 1230  756  402  861  320  465  465  465
# [61]  900  126  867  867  102  102  338  534 1311  954 1143  249  486   75  408
# [76]  573  687  570  570  570  570  570  570  570  570  570  570  570  570  570
# [91]  570  570  570  570  570
table(getLength(cancer_transcript))
# 
#   75   99  102  108  126  138  210  213  249  267  278  320  338  342  402  408 
#    1    1    2    1    1    1    1    1    1    1    1    1    1    1    1    1 
#  411  465  468  486  507  534  543  570  573  633  687  729  756  795  861  867 
#    2    3    1    1    1    1    3   18    1    2    1    2    1    1    1   12 
#  900  954  975 1143 1230 1245 1311 1473 1578 1647 1674 1707 1821 1890 1926 1974 
#    1    1    1    1    1    1    1    1    1    1    1    1    1    1    1    1 
# 2031 2073 2103 2142 2151 2169 2334 2454 2475 2520 
#    1    1    1    2    1    1    1    1    1    1 
lengths <- table(getLength(cancer_transcript))
barplot(lengths,xlab = "Cancer Lranscripts Lengths",ylab = "Frequency")
```

![](https://upload-images.jianshu.io/upload_images/14383117-b5f5fd02611db314.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 3.2 GC含量火山图

```R
cancer_tsGC <- unlist(lapply(cancer_transcript, GC))
hist(cancer_tsGC)
```

![](https://upload-images.jianshu.io/upload_images/14383117-941a75bc3fae0fd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 以为这就学会惹，这时作者出现在墙角: "*ce n'est que le premier pas qui coûte.*"
>
> It is only the first step that costs.

## References

- **Version 3.1-5 of the seqinR manual** <http://seqinr.r-forge.r-project.org/seqinr_3_1-5.pdf>
- **DAVE TANG'S BLOG** Using the R SeqinR package <https://davetang.org/muse/2013/05/09/using-the-r-seqinr-package/>
- **DNA Sequence Statistics (1)** <https://a-little-book-of-r-for-bioinformatics.readthedocs.io/en/latest/src/chapter1.html#gc-content-of-dna>
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)

