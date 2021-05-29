> Biostrings 从开始到~~放弃~~轰狂
![](https://upload-images.jianshu.io/upload_images/14383117-bdb6e1161ae2ec46.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##  Pairwise Sequence Alignments 

## 1. 函数 `pairwiseAlignment()` 

> This function aligns a set of *pattern* strings to a *subject* string in a global, local, or overlap (ends-free) fashion with or without affine gaps using either a fixed or quality-based substitution scoring scheme.

## 2. 应用

### 2.1 进化模型在蛋白质比对中的应用

众所周知蛋白质都是同一个祖先传下来的，所以进化模型对于序列两两比对是有指导作用的，最常见的两个蛋白质进化模型就是 **PAM矩阵** 和 **BlLOSUM矩阵**，而 `Biostrings` 包内也自带了5个 PAM 和5个 BLOSUM 矩阵 (PAM30 PAM40, PAM70, PAM120, PAM250, BLOSUM45, BLOSUM50, BLOSUM62, BLOSUM80, BLOSUM100) ，可以用于函数 `pairwiseAlignment()` 的参数 `substitutionMatrix` .

### 2.2 移除reads中的接头 (adapters)

众所周知接头序列是会被一起测序的，所以需要找到它们并~~毁灭~~删掉它们。作者有好几页高级且美丽的代码完成了这件事。

### 2.3 测序实验的质量确认序列两两比对

函数 `pairwiseAlignment()` 是很灵活极具可塑性的，灵活到可以诊断出当 `matchPDict()`和其他相关函数找不到匹配序列时的问题。

## 3. 示例

不知道问什么就想试试 **2.3** (ˉ﹃ˉ)

作者用一个实验里的 ***Φ* X174 DNA** 和 **GenBank NC 001422** 做比对，有了以下操作：

```R
data(phiX174Phage)  ## GenBank的
genBankPhage <- phiX174Phage[[1]] 
nchar(genBankPhage)  
# [1] 5386  
data(srPhiX174) ## 实验里的，有~5000个 unique short reads
srPhiX174
#   A DNAStringSet instance of length 1113
#        width seq
#    [1]    35 GTTATTATACCGTCAAGGACTGTGTGACTATTGAC
#    [2]    35 GGTGGTTATTATACCGTCAAGGACTGTGTGACTAT
#    [3]    35 TACCGTCAAGGACTGTGTGACTATTGACGTCCTTC
#    [4]    35 GTACGCCGGGCAATAATGTTTATGTTGGTTTCATG
#    [5]    35 GGTTTCATGGTTTGGTCTAACTTTACCGCTACTAA
#    ...   ... ...
# [1109]    35 ATAATGTTTATGTTGGTTTCATGGTTTGTTCTATC
# [1110]    35 GGGCAATAATGTTTATGTTGGTTTCATTTTTTTTT
# [1111]    35 CAATAATGTTTATGTTGGTTTCATGGTTTGTTTTA
# [1112]    35 GACGTCCTTCCTCGTACGCCGGGCAATGATGTTTA
# [1113]    35 ACGCCGGGCAATAATGTTTATGTTGTTTTCATTGT
quPhiX174   ## srPhiX174 的 quality measures
#   A BStringSet instance of length 1113
#        width seq
#    [1]    35 ZYZZZZZZZZZYYZZYYYYYYYYYYYYYYYYYQYY
#    [2]    35 ZZYZZYZZZZYYYYYYYYYYYYYYYYYYYVYYYTY
#    [3]    35 ZZZYZYYZYYZYYZYYYYYYYYYYYYYYVYYYYYY
#    [4]    35 ZZYZZZZZZZZZYZTYYYYYYYYYYYYYYYYYNYT
#    [5]    35 ZZZZZZYZYYZZZYYYYYYYYYYYYYYYYYSYYSY
#    ...   ... ...
# [1109]    35 ZZZZZYZZZYZYZZVYYYYVYYYQYYYQCYQYQCT
# [1110]    35 YYYYTYYYYYTYYYYYYYYTJTTYOAYIIYYYGAY
# [1111]    35 ZZYZZZZZZZZZZVZYYVYYYYYYVQYYYIQYAYW
# [1112]    35 YZYZZYYYZYYYYYYVYYVYYYYWWVYYYYYWYYV
# [1113]    35 ZZYYZYYYYYYZYVZYYYYYYVYYJAYYYIGYCJY
summary(wtPhiX174)  ## unique short reads 的频率
#    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#    2.00    2.00    3.00   48.34    6.00  965.00 
fullShortReads <- rep(srPhiX174, wtPhiX174)  ## (short read)*freq
srPDict <- PDict(fullShortReads) 
table(countPDict(srPDict, genBankPhage)) ## 与GenBank的基因组做对比
```

和 `countPDict()` 不同，`pairwiseAlignment()` 直接作用于原始序列。对于特殊序列，作者更推荐使用 `pairwiseAlignment()`, 同时序列的频率则应用于 `summary()` , `mismatchSummary()` , `coverage()` 等函数的参数 `weight` .

随机取GenBank基因组的substring做练习：

```R
genBankSubstring <- substring(genBankPhage, 2793-34, 2811+34) 
genBankAlign <- 
  pairwiseAlignment(srPhiX174,genBankSubstring,
                    patternQuality = SolexaQuality(quPhiX174),
                    subjectQuality = SolexaQuality(99L),
                    type = "global-local")
summary(genBankAlign,weight = wtPhiX174)
# Global-Local Single Subject Pairwise Alignments
# Number of Alignments:  53802
# 
# Scores:
#   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
# -45.08   35.81   50.07   41.24   59.50   67.35 
# 
# Number of matches:
#   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
# 21.00   31.00   33.00   31.46   34.00   35.00 
# 
# Top 10 Mismatch Counts:
#   SubjectPosition Subject Pattern Count Probability
# 1               53       C       T 22965  0.95536234
# 2               35       C       T 22849  0.99969373
# 3               76       G       T  1985  0.10062351
# 4               69       A       T  1296  0.05654697
# 5               79       C       T  1289  0.07289899
# 6               58       A       C  1153  0.04783637
# 7               72       G       A  1130  0.05248978
# 8               63       G       A  1130  0.04767731
# 9               67       T       G  1130  0.04721514
# 10              81       A       G  1103  0.06672313
```

对 **比对到Φ X174基因组的短序列** 作图：

```R
genBankCoverage <- coverage(genBankAlign, weight = wtPhiX174) 
plot((2793-34):(2811+34), as.integer(genBankCoverage),
     xlab = "Position",ylab = "Coverage",
     type = "l") 
```

![](https://upload-images.jianshu.io/upload_images/14383117-6bfad656e704f283.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## References

- **Pairwise Sequence Alignments** https://bioconductor.org/packages/release/bioc/vignettes/Biostrings/inst/doc/PairwiseAlignments.pdf
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
