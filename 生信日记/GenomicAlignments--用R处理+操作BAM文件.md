> 愿天堂没有头痛
![](https://upload-images.jianshu.io/upload_images/14383117-32b52af43bf4fed0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 短序列比对的呈现和操纵

> Lawrence M, Huber W, Pagès H, Aboyoun P, Carlson M, Gentleman R, Morgan M and Carey V (2013). “Software for Computing and Annotating Genomic Ranges.”*PLoS Computational Biology*, **9**. <http://dx.doi.org/10.1371/journal.pcbi.1003118>, 

## 1. 关于 `GenomicAlignments` 

`GenomicAlignments` 可以高效储存和操作短序列比对 (short genomic alignments) ，包括 read counting, computing the coverage, junction detection, 以及比对中核苷酸含量的操作。包内有 `GAlignments` , `GAlignmentPairs` , `GAlignmentsList` 三种对象。

## 2. 访问序列信息

### 2.1 载入 BAM 文件

从 BAM 文件得到已比对的 reads 和其序列。

这时用到了一个数据包：`RNAseqData.HNRNPC.bam.chr14` .
```R
library(GenomicAlignments)
library(RNAseqData.HNRNPC.bam.chr14)
bamfiles <- RNAseqData.HNRNPC.bam.chr14_BAMFILES 
names(bamfiles)
# [1] "ERR127306" "ERR127307" "ERR127308" "ERR127309" "ERR127302"
# [6] "ERR127303" "ERR127304" "ERR127305"
```

调用 `Rsamtools` 包内的函数 `quickBamFlagSummary()` 查看 BAM 文件中的序列是单端或双端比对。

```R
library(Rsamtools)
quickBamFlagSummary(bamfiles[1], main.groups.only=TRUE) 
#                                 group |    nb of |    nb of | mean / max
#                                    of |  records |   unique | records per
#                               records | in group |   QNAMEs | unique QNAME
# All records........................ A |   800484 |   393300 | 2.04 / 10
#   o template has single segment.... S |        0 |        0 |   NA / NA
#   o template has multiple segments. M |   800484 |   393300 | 2.04 / 10
#       - first segment.............. F |   400242 |   393300 | 1.02 / 5
#       - last segment............... L |   400242 |   393300 | 1.02 / 5
#       - other segment.............. O |        0 |        0 |   NA / NA
# 
# Note that (S, M) is a partitioning of A, and (F, L, O) is a partitioning of M.
# Indentation reflects this.
```

在利用 `readGAlignments()` 读取基因组比对前，需要用函数 `ScanBamParam()` 构建一些参数。

```R
flag1 <- scanBamFlag(isFirstMateRead=TRUE, 
                     isSecondMateRead=FALSE,
                     isDuplicate=FALSE, 
                     isNotPassingQualityControls=FALSE) 
## 作者在这里为了简化后面的流程，只选择了对应于第1个segment的比对序列
param1 <- ScanBamParam(flag=flag1, what="seq") 
```
```R
gal1 <- readGAlignments(bamfiles[1], use.names=TRUE, param=param1) 
class(gal1)  ## 获得了一个GAlignments对象
# [1] "GAlignments"
# attr(,"package")
# [1] "GenomicAlignments"
gal1
# GAlignments object with 400242 alignments and 1 metadata column:
#                      seqnames strand       cigar    qwidth     start
#                         <Rle>  <Rle> <character> <integer> <integer>
#   ERR127306.26333541    chr14      +         72M        72  19069583
#   ERR127306.12926170    chr14      -         72M        72  19363755
#   ERR127306.26974899    chr14      +         72M        72  19369799
#    ERR127306.4055622    chr14      +         72M        72  19411097
#   ERR127306.13938407    chr14      +         72M        72  19411459
#                  ...      ...    ...         ...       ...       ...
#   ERR127306.10965452    chr14      -         72M        72 106981361
#   ERR127306.11919172    chr14      -         72M        72 106986528
#    ERR127306.3567919    chr14      +         72M        72 106989680
#   ERR127306.21510817    chr14      +         72M        72 106994763
#     ERR127306.661203    chr14      -         72M        72 107003171
#                            end     width     njunc |
#                      <integer> <integer> <integer> |
#   ERR127306.26333541  19069654        72         0 |
#   ERR127306.12926170  19363826        72         0 |
#   ERR127306.26974899  19369870        72         0 |
#    ERR127306.4055622  19411168        72         0 |
#   ERR127306.13938407  19411530        72         0 |
#                  ...       ...       ...       ... .
#   ERR127306.10965452 106981432        72         0 |
#   ERR127306.11919172 106986599        72         0 |
#    ERR127306.3567919 106989751        72         0 |
#   ERR127306.21510817 106994834        72         0 |
#     ERR127306.661203 107003242        72         0 |
#                                          seq
#                               <DNAStringSet>
#   ERR127306.26333541 TGAGAATGAT...ATGGCTGCAT
#   ERR127306.12926170 CCCCAGGTAT...TGGACACCAG
#   ERR127306.26974899 CATAGATGCA...TAACTTACCA
#    ERR127306.4055622 TGCTGGTGCA...CAGGAATCAG
#   ERR127306.13938407 CAGGAGGTAG...CTGTCTGGTC
#                  ...                     ...
#   ERR127306.10965452 GGGAGGCCCT...TGGAGAGAAC
#   ERR127306.11919172 CCTGAGAGCC...TCACTCTCTG
#    ERR127306.3567919 CAACTTTTAT...AGCAGGGAGG
#   ERR127306.21510817 CAAAGCTGGA...TTTTGCTGCC
#     ERR127306.661203 CATGACTTGA...TACAGTATTT
#   -------
#   seqinfo: 93 sequences from an unspecified genome
```

### 2.2 各类基础访问函数

```R
seqnames(gal1)
# factor-Rle of length 400242 with 1 run
#   Lengths: 400242
#   Values :  chr14
# Levels(93): chr1 chr10 chr11 ... chrUn_gl000249 chrX chrY
```

```R
head(seqlevels(gal1))
# [1] "chr1"                  "chr10"                
# [3] "chr11"                 "chr11_gl000202_random"
# [5] "chr12"                 "chr13"        
```

```R
strand(gal1)
# factor-Rle of length 400242 with 111564 runs
#   Lengths:  1  1 15 20  2  2  2  3  1  2 ...  1  3  3  1  2  2  2  2  1
#   Values :  +  -  +  -  +  -  +  -  +  - ...  -  +  -  +  -  +  -  +  -
# Levels(3): + - *
```

```R
head(cigar(gal1))
# [1] "72M" "72M" "72M" "72M" "72M" "72M"
```

```R
head(njunc(gal1))
# [1] 0 0 0 0 0 0
```

## 3. 处理已比对核苷酸序列

当由高通量测序实验产生的 reads 被比对到参考基因组后，一般来说人们提出的问题分为两大类: **positional only** 和 **nucleotide-related**.

> **Positional only** questions are about the position of the alignments with respect to the reference genome.
>
> **Nucleotide-related** questions are about the nucleotide content of the alignments. 

针对比对的 "nucleotide-related" 问题， `GenomicAlignments` 提供了不同层次的工具。

### 3.1 获得原始序列

BAM 格式中的 read 序列是“反向互补”的，当它们与反义链比对时，我们需要将它们重新“反向互补”，得到原始询问序列 (*original query sequences*).

确定需要被“反向互补”的 reads:

```R
mcols(gal1)$seq 
#   A DNAStringSet instance of length 400242
#          width seq
#      [1]    72 TGAGAATGATGATTTCCAATTTCATCC...TGAACTCATCATTTTTTATGGCTGCAT
#      [2]    72 CCCCAGGTATACACTGGACTCCAGGTG...GATACACACACTCAAGGTGGACACCAG
#      [3]    72 CATAGATGCAAGAATCCTCAATCAAAT...ACAGCACATTAAAAAGATAACTTACCA
#      [4]    72 TGCTGGTGCAGGATTTATTCTACTAAG...AATCCACTTTCTTATCTCAGGAATCAG
#      [5]    72 CAGGAGGTAGGCTGTGCGTTCAGCAGT...CTGGTTGATCACCTTGACTGTCTGGTC
#      ...   ... ...
# [400238]    72 GGGAGGCCCTTTATATAACCATCAGGT...CTAATAGGATAAAAGCATGGAGAGAAC
# [400239]    72 CCTGAGAGCCCCTTGCTGTCCTGAGCA...GCCCTCTGGTGTTCTGATCACTCTCTG
# [400240]    72 CAACTTTTATTTCTTAAACACAAGACA...TCTCAGGTGAGCTGTCGAGCAGGGAGG
# [400241]    72 CAAAGCTGGATGTGTCTAGTGTTTTTA...AATAAGAGCATGTGTGGTTTTGCTGCC
# [400242]    72 CATGACTTGATGGCTGGAACAAATACA...AATACTAGCCTTTGCCATACAGTATTT
oqseq1 <- mcols(gal1)$seq 
is_on_minus <- as.logical(strand(gal1) == "-") 
oqseq1[is_on_minus] <- reverseComplement(oqseq1[is_on_minus]) 
```

每个 read 都会被比对不止一次，所以 `gal1` 中肯定有重复。

```R
is_dup <- duplicated(names(gal1))
table(is_dup)
# is_dup
#  FALSE   TRUE 
# 393300   6942 
```

去重：

```R
oqseq1 <- oqseq1[!is_dup] 
length(oqseq1)  ## 检查一下
# [1] 393300
length(gal1)
# [1] 400242
```

### 3.2 错配、插入缺失标记、缺口 (Mismatches, indels, and gaps )

由于比对过程中是容许一些错配、插入缺失标记、缺口的，所以 `mcols(gal1)$seq` 中的序列并不是完全与参考基因组匹配的。

"CIGAR" 包含着这些“小错误”出现在比对中的位置信息。

> The ‘CIGAR’ (Compact Idiosyncratic Gapped Alignment Report) string is how the SAM/BAM format represents spliced alignments. 
>
> For CIGAR =’6M’, this means there are 6 exact matches to the reference.

![](https://upload-images.jianshu.io/upload_images/14383117-f65d051c7c7e6053.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
head(sort(table(cigar(gal1)), decreasing=TRUE)) 
# 
#        72M 35M123N37M  64M316N8M 38M670N34M 18M123N54M  2M131N70M 
#     301920        134        134        133        119         96 
colSums(cigarOpTable(cigar(gal1)))   ## 总览
#         M         I         D         N         S         H         P 
#  28815928      1496       818 330945002         0         0         0 
#         =         X 
#         0         0 
table(njunc(gal1))   ## gap的数量
# 
#      0      1      2      3 
# 303622  94557   2052     11 
```



## References

- **An Introduction to the GenomicAlignments Package** 

  https://bioconductor.org/packages/release/bioc/vignettes/GenomicAlignments/inst/doc/GenomicAlignmentsIntroduction.pdf

- **Working with aligned nucleotides**

  https://bioconductor.org/packages/release/bioc/vignettes/GenomicAlignments/inst/doc/WorkingWithAlignedNucleotides.pdf

- **JEFworks**: Cigar Strings For Dummies

  <https://jef.works/blog/2017/03/28/CIGAR-strings-for-*dummies*/>

- **Bioinformatics I/O**: Java CIGAR Parser for SAM format

  <http://bioinformatics.cvr.ac.uk/blog/tag/cigar-string/>
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
