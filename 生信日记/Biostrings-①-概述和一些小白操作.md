>![](https://upload-images.jianshu.io/upload_images/14383117-c038392aa05ef134.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 高效操作生物序列 (biological strings)

> Pagès H, Aboyoun P, Gentleman R, DebRoy S (2019). *Biostrings: Efficient manipulation of biological strings*. R package version 2.52.0.
>
> DOI: [10.18129/B9.bioc.Biostrings](https://doi.org/doi:10.18129/B9.bioc.Biostrings)

## 1. 关于 `Biostring` 

`Biostrings` 包含了很多 **类 (class)** 和 **函数 (function)**，用于表示/呈现生物序列，**如DNA, RNA, 氨基酸**。同时 `Biostrings` 还具有**模式匹配 (pattern-matching)** 和**成对序列比对 (pairwise alignment)** 功能，以及读取和输出 FASTA 等格式文件的函数。

![](https://upload-images.jianshu.io/upload_images/14383117-d30c534acb19260b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2. The *Xstring* class 

> The `XString` is in fact a virtual class and therefore cannot be instanciated.
>
> The `XString` class allows us to create, store, and work with different types of strings. We are only allowed to work with subclasses of `XString`: `BString`, `DNAString`, `RNAString`, and `AAString`.

### 2.1 创建一个对象

### 2.1.1 `XString` 

```r
library(Biostrings)
dna1 <- DNAString("ACG-N")
dna1
#   5-letter "DNAString" instance
# seq: ACG-N
```

### 2.2.2 `XStringSet`  

试错：如果输入 "ATCG (IUPAC codes) " 以外的字母：

```R
DNAStringSet("ADE")
# Error in .Call2("new_XString_from_CHARACTER", class(x0), string, start,  : 
#   key 69 (char 'E') not in lookup table
AAStringSet("ADE")
#   A AAStringSet instance of length 1
#     width seq
# [1]     3 ADE
```

建立一个有3条序列的对象：

```R
dna2 <- DNAStringSet(c("ACGT", "GTCA", "GCTA"))
dna2
#   A DNAStringSet instance of length 3
#     width seq
# [1]     4 ACGT
# [2]     4 GTCA
# [3]     4 GCTA
```

### 2.2.3 IUPAC codes 

`DNAString` 的字母表实际不止 "ATCG" 。"N" 作为插入 (insertion) , "M" 可以代表 "A" 或 "C"。

```R
IUPAC_CODE_MAP
#      A      C      G      T      M      R      W      S 
#    "A"    "C"    "G"    "T"   "AC"   "AG"   "AT"   "CG" 
#      Y      K      V      H      D      B      N 
#   "CT"   "GT"  "ACG"  "ACT"  "AGT"  "CGT" "ACGT" 
```

### 2.2.4 `[`

```R
dna1[1:3]
#   3-letter "DNAString" instance
# seq: ACG

dna2[2:3]
#   A DNAStringSet instance of length 2
#     width seq
# [1]     4 GTCA
# [2]     4 GCTA
```

### 2.2.5 `[[`

 ```R
dna2[[1]]
#   4-letter "DNAString" instance
# seq: ACGT
 ```

### 2.2.6 给序列命名

```R
names(dna2) <- paste0("seq", 1:3)
dna2
#   A DNAStringSet instance of length 3
#     width seq                           names               
# [1]     4 ACGT                          seq1
# [2]     4 GTCA                          seq2
# [3]     4 GCTA                          seq3
```

### 2.2.7 全部的 `string classes`

> - `DNAString[Set]`: DNA sequences.
> - `RNAString[Set]`: RNA sequences.
> - `AAString[Set]`: Amino Acids sequences (protein).
> - `BString[Set]`: “Big” sequences, using any kind of letter.

## 3. 基本功能&生物学功能

### 3.1 基本功能

> - `length`, `names`.
> - `c` and `rev` (reverse the sequence).
> - `width`, `nchar` (number of characters in each sequence).
> - `==`, `duplicated`, `unique`.
> - `as.charcater` or `toString`: converts to a base `character()` vector.
> - `sort`, `order`.
> - `chartr`: convert some letters into other letters.
> - `subseq`, `subseq<-`, `extractAt`, `replaceAt`.
> - `replaceLetterAt`.

```R
rev(dna1)
#   5-letter "DNAString" instance
# seq: N-GCA
rev(dna2)
#   A DNAStringSet instance of length 3
#     width seq                           names               
# [1]     4 GCTA                          seq3
# [2]     4 GTCA                          seq2
# [3]     4 ACGT                          seq1
```

```r
chartr("N","T",dna1)
#   5-letter "DNAString" instance
# seq: ACG-T
```

### 3.2 生物学功能

> - `reverse`: reverse the sequence.
> - `complement`, `reverseComplement`: (reverse) complement the sequence.
> - `translate`: translate the DNA or RNA sequence into amino acids

```R
translate(dna2)
#   A AAStringSet instance of length 3
#     width seq                           names               
# [1]     1 T                             seq1
# [2]     1 V                             seq2
# [3]     1 A                             seq3
```

```R
dna3 <- DNAString("ATGCAGTCGMTCD")
complement(dna3)
#   13-letter "DNAString" instance
# seq: TACGTCAGCKAGH
reverseComplement(dna3)
#   13-letter "DNAString" instance
# seq: HGAKCGACTGCAT
```

## 4. 对序列计数

包内有一系列函数可以求出 **序列内的GC含量、序列内核苷酸的频率、已比对序列的位置权重矩阵**。

> - `alphabetFrequency`, `letterFrequency`: Compute the frequency of all characters (`alphabetFrequency`) or only specific letters (`letterFrequency`).
> - `dinucleotideFrequency`, `trinucleotideFrequency`, `oligonucleotideFrequeny`: compute frequencies of dinucleotides (2 bases), trinucleotides (3 bases) and oligonucleotides (general number of bases).
> - `letterFrequencyInSlidingView`: letter frequencies, but in sliding views along the string.
> - `consensusMatrix`: consensus matrix; almost a position weight matrix.

```R
alphabetFrequency(dna1)
# A C G T M R W S Y K V H D B N - + . 
# 1 1 1 0 0 0 0 0 0 0 0 0 0 0 1 1 0 0 
alphabetFrequency(dna2)
#      A C G T M R W S Y K V H D B N - + .
# [1,] 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
# [2,] 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
# [3,] 1 1 1 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0
```

```R
letterFrequency(dna2, "GC")
#      G|C
# [1,]   2
# [2,]   2
# [3,]   2
```

```R
consensusMatrix(dna2,as.prob = TRUE) 
## If TRUE then probabilities are reported, otherwise counts (the default)
#        [,1]      [,2]      [,3]      [,4]
# A 0.3333333 0.0000000 0.0000000 0.6666667
# C 0.0000000 0.6666667 0.3333333 0.0000000
# G 0.6666667 0.0000000 0.3333333 0.0000000
# T 0.0000000 0.3333333 0.3333333 0.3333333
# M 0.0000000 0.0000000 0.0000000 0.0000000
# R 0.0000000 0.0000000 0.0000000 0.0000000
# W 0.0000000 0.0000000 0.0000000 0.0000000
# S 0.0000000 0.0000000 0.0000000 0.0000000
# Y 0.0000000 0.0000000 0.0000000 0.0000000
# K 0.0000000 0.0000000 0.0000000 0.0000000
# V 0.0000000 0.0000000 0.0000000 0.0000000
# H 0.0000000 0.0000000 0.0000000 0.0000000
# D 0.0000000 0.0000000 0.0000000 0.0000000
# B 0.0000000 0.0000000 0.0000000 0.0000000
# N 0.0000000 0.0000000 0.0000000 0.0000000
# - 0.0000000 0.0000000 0.0000000 0.0000000
# + 0.0000000 0.0000000 0.0000000 0.0000000
# . 0.0000000 0.0000000 0.0000000 0.0000000
```

## References

- **Coursera: Algorithms for DNA Sequencing** 

- **Biostrings** *Kasper D. Hansen*

   <https://kasperdanielhansen.github.io/genbioconductor/html/Biostrings.html>

- **Lab 1: Biostrings in R**

   https://web.stanford.edu/class/bios221/labs/biostrings/lab_1_biostrings.html
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
