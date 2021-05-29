>![](https://upload-images.jianshu.io/upload_images/14383117-513cb1e496602b5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 利用寡核苷酸微阵列报告序列 (microarray reporter sequence) 信息进行预处理和质检

## 1. 概述

 `hgu95av2probe` 含有 Affymetrix' *HgU95a version 2* 基因芯片的微阵列报告序列，以此为例进行操作。

```r  
library(Biostrings)
library(hgu95av2probe)
library(hgu95av2cdf) 
```

## 2. Using probe packages 

### 2.1 探针配对

```R
pm <- DNAStringSet(hgu95av2probe)
pm
#   A DNAStringSet instance of length 201800
#          width seq
#      [1]    25 TGGCTCCTGCTGAGGTCCCCTTTCC
#      [2]    25 GGCTGTGAATTCCTGTACATATTTC
#      [3]    25 GCTTCAATTCCATTATGTTTTAATG
#      [4]    25 GCCGTTTGACAGAGCATGCTCTGCG
#      [5]    25 TGACAGAGCATGCTCTGCGTTGTTG
#      ...   ... ...
# [201796]    25 AGATGGATAGCCTTCTGTCAAAGCA
# [201797]    25 ATAGCCTTCTGTCAAAGCATCATCT
# [201798]    25 TTCTGTCAAAGCATCATCTCAACAA
# [201799]    25 CAAAGCATCATCTCAACAAGCCCTC
# [201800]    25 GTGCTCCTTGTCAACAGCGCACCCA
```

随机取200个探针，储存为 `pDict ` 对象，统计其出现的频率。 

```R
dict <- pm[3801:4000] 
pdict <- PDict(dict) 
m <- vcountPDict(pdict, pm) 
dim(m)
# [1] 200 201800
```

> `Dict()` : The constructor function for creating new `PDict` objects,  containers for storing a preprocessed dictionary of DNA patterns.
>
> `vcountpDict()` : Vectorized versions of `countPDict()`, returns the "how many times" information i.e. the number of occurrences for each pattern.

```r
rowSums(m)
#   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 1 2 2 1 1 1 2 1 2
#  [28] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
#  [55] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 3 2 3 3 3
#  [82] 3 3 2 2 2 1 1 1 1 2 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
# [109] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
# [136] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2
# [163] 2 2 1 2 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
# [190] 1 1 1 1 1 1 1 1 1 1 1
table(rowSums(m)) 
# 
#   1   2   3 
# 179  15   6 
```

找出出现次数为3的元素坐标：

```R
which(rowSums(m) == 3) 
# [1] 77 79 80 81 82 83
```

找出对应序列：

```R
ii <- which(m[77, ] != 0) 
ii
# [1]   3877  59741 103203
> pm[ii]
#   A DNAStringSet instance of length 3
#     width seq
# [1]    25 TCGTCATCAGGTGCATAGCAAGTGA
# [2]    25 TCGTCATCAGGTGCATAGCAAGTGA
# [3]    25 TCGTCATCAGGTGCATAGCAAGTGA
```

### 2.2 各碱基含量

```R
bcpm <- alphabetFrequency(pm, baseOnly=TRUE) 
## the returned vector (or matrix) only contains the frequencies of the letters that belong to the "base" alphabet
head(bcpm)
#      A  C G  T other
# [1,] 1 10 6  8     0
# [2,] 5  5 5 10     0
# [3,] 6  4 3 12     0
# [4,] 4  7 8  6     0
# [5,] 4  5 8  8     0
# [6,] 2  7 6 10     0
alphabetFrequency(pm, baseOnly=TRUE, collapse=TRUE)  ## 统计各碱基在全部探针的含量
#       A       C       G       T   other 
# 1250858 1235532 1186629 1371981       0 
```

## 3. “预处理及质量”作图

### 3.1 探针GC含量 barplot

```R
GC = ordered(bcpm[,"G"] + bcpm[,"C"]) 
colores = rainbow(nlevels(GC)) 
barplot(table(GC), col=colores, xlab="GC", ylab="number")
```

![](https://upload-images.jianshu.io/upload_images/14383117-1cdda8d3b70009b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> *于是并没有做 hgu95av2 AffyBatch 的 indices 的比对，没有画 intensity-GC content 的boxplot，因为 **看，不，懂  (sad.***
## References
- **Handling probe sequence information** https://bioconductor.org/packages/release/bioc/vignettes/Biostrings/inst/doc/matchprobes.pdf
- **.CEL File Extension** <https://fileinfo.com/extension/cel>
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
