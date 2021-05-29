>![](https://upload-images.jianshu.io/upload_images/14383117-8fe0c0639c570dcf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## MultipleAlignment 对象

## 1. `MultipleAlignment` classes

`DNAMultipleAlignment` ,  `RNAMultipleAlignment`, `AAMultipleAlignment` 可以将已比对的 DNA, RNA, 氨基酸序列作为一个单独的对象表现出来，并可以对其进行 masking.

> **Masking:**  Converting a DNA sequence [A,C,G,T] (usually repetitive or of low quality) to the uninformative character state N or to lower case characters [a,c,g,t] (soft masking).

## 2. 创建和Masking

### 2.1 创建 `MultipleAlignment` 对象

`Biostrings` 含有可以读取 clustaW, Phylip, Stolkholm 格式文件的函数。

用包内自带数据尝试一波：

```R
origMAlign <- 
  readDNAMultipleAlignment(filepath = system.file("extdata",
                                                  "msx2_mRNA.aln",
                                                  package = "Biostrings"),
                           format = "clustal")
```

```R
phylipMAlign <- 
  readAAMultipleAlignment(filepath = system.file("extdata",
                                                 "Phylip.txt",
                                                 package = "Biostrings"),
                          format = "phylip")
```

更改 `origMAlign` 的行名：

```R
origMAlign
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] -----TCCCGTCTCCGCAGC...ATTAAAAAAAAAAAAAAAAA gi|84452153|ref|N...
# [2] --------------------...-------------------- gi|208431713|ref|...
# [3] --------------------...-------------------- gi|118601823|ref|...
# [4] --------------------...-------------------- gi|114326503|ref|...
# [5] --------------------...-------------------- gi|119220589|ref|...
# [6] --------------------...-------------------- gi|148540149|ref|...
# [7] --------------CGGCTC...-------------------- gi|45383056|ref|N...
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- gi|213515133|ref|...
rownames(origMAlign)
# [1] "gi|84452153|ref|NM_002449.4|"   "gi|208431713|ref|NM_001135625."
# [3] "gi|118601823|ref|NM_001079614." "gi|114326503|ref|NM_013601.2|" 
# [5] "gi|119220589|ref|NM_012982.3|"  "gi|148540149|ref|NM_001003098."
# [7] "gi|45383056|ref|NM_204559.1|"   "gi|213515133|ref|NM_001141603."
rownames(origMAlign) <- c("Human","Chimp","Cow","Mouse","Rat",
                           "Dog","Chicken","Salmon")
origMAlign
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] -----TCCCGTCTCCGCAGC...ATTAAAAAAAAAAAAAAAAA Human
# [2] --------------------...-------------------- Chimp
# [3] --------------------...-------------------- Cow
# [4] --------------------...-------------------- Mouse
# [5] --------------------...-------------------- Rat
# [6] --------------------...-------------------- Dog
# [7] --------------CGGCTC...-------------------- Chicken
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- Salmon
```

展示细节：

```R
detail(origMAlign) 
```

![](https://upload-images.jianshu.io/upload_images/14383117-160d2f35a0d8a7e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2 Masking

分别使用函数 `rowmask()` 和 `colmask()` ：

```R
maskTest <- origMAlign 
rowmask(maskTest) <- IRanges(start=1,end=3)  ## operations to mask rows
rowmask(maskTest) 
# NormalIRanges object with 1 range and 0 metadata columns:
#           start       end     width
#       <integer> <integer> <integer>
#   [1]         1         3         3
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] ####################...#################### Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] --------------------...-------------------- Mouse
# [5] --------------------...-------------------- Rat
# [6] --------------------...-------------------- Dog
# [7] --------------CGGCTC...-------------------- Chicken
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- Salmon
colmask(maskTest) <- IRanges(start=c(1,1000),end=c(500,2343)) 
colmask(maskTest)
# NormalIRanges object with 2 ranges and 0 metadata columns:
#           start       end     width
#       <integer> <integer> <integer>
#   [1]         1       500       500
#   [2]      1000      2343      1344
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] ####################...#################### Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] ####################...#################### Mouse
# [5] ####################...#################### Rat
# [6] ####################...#################### Dog
# [7] ####################...#################### Chicken
# [8] ####################...#################### Salmon
```

通过赋值 'NULL'，移除被 mask 的行和列：

```R
rowmask(maskTest) <- NULL 
rowmask(maskTest)
# NormalIRanges object with 0 ranges and 0 metadata columns:
#        start       end     width
#    <integer> <integer> <integer>
colmask(maskTest) <- NULL 
colmask(maskTest)
# NormalIRanges object with 0 ranges and 0 metadata columns:
#        start       end     width
#   <integer> <integer> <integer>
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] -----TCCCGTCTCCGCAGC...ATTAAAAAAAAAAAAAAAAA Human
# [2] --------------------...-------------------- Chimp
# [3] --------------------...-------------------- Cow
# [4] --------------------...-------------------- Mouse
# [5] --------------------...-------------------- Rat
# [6] --------------------...-------------------- Dog
# [7] --------------CGGCTC...-------------------- Chicken
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- Salmon
```

通过参数 `invert=TRUE` ，可以设定要留下的行和列，并能得到与之前的操作完全一致的mask：

```R
rowmask(maskTest, invert=TRUE) <- IRanges(start=4,end=8)  
## IRange的参数也与之前的相反/互补
rowmask(maskTest) 
# NormalIRanges object with 1 range and 0 metadata columns:
#           start       end     width
#       <integer> <integer> <integer>
#   [1]         1         3         3
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] ####################...#################### Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] --------------------...-------------------- Mouse
# [5] --------------------...-------------------- Rat
# [6] --------------------...-------------------- Dog
# [7] --------------CGGCTC...-------------------- Chicken
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- Salmon
colmask(maskTest, invert=TRUE) <- IRanges(start=501,end=999)  ##列的操作同上
colmask(maskTest)
# NormalIRanges object with 2 ranges and 0 metadata columns:
#           start       end     width
#       <integer> <integer> <integer>
#   [1]         1       500       500
#   [2]      1000      2343      1344
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] ####################...#################### Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] ####################...#################### Mouse
# [5] ####################...#################### Rat
# [6] ####################...#################### Dog
# [7] ####################...#################### Chicken
# [8] ####################...#################### Salmon
```

移除mask:

```R
colmask(maskTest) <- NULL 
rowmask(maskTest) <- NULL 
```

针对参数 `append=` , 进行不同的设置和操作：

> The *append* argument takes *union*, *replace* or *intersect* to indicate how to combine the new value with rowmask(x),

```R
## 默认状态，mask全部
rowmask(maskTest) <- IRanges(start=1,end=3)
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] ####################...#################### Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] --------------------...-------------------- Mouse
# [5] --------------------...-------------------- Rat
# [6] --------------------...-------------------- Dog
# [7] --------------CGGCTC...-------------------- Chicken
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- Salmon

## append="intersect", mask the rows that intersect with masked rows
rowmask(maskTest,append="intersect") <- IRanges(start=2,end=5)
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] -----TCCCGTCTCCGCAGC...ATTAAAAAAAAAAAAAAAAA Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] --------------------...-------------------- Mouse
# [5] --------------------...-------------------- Rat
# [6] --------------------...-------------------- Dog
# [7] --------------CGGCTC...-------------------- Chicken
# [8] GGGGGAGACTTCAGAAGTTG...-------------------- Salmon

## append="replace"
rowmask(maskTest,append="replace") <- IRanges(start=5,end=8) 
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] -----TCCCGTCTCCGCAGC...ATTAAAAAAAAAAAAAAAAA Human
# [2] --------------------...-------------------- Chimp
# [3] --------------------...-------------------- Cow
# [4] --------------------...-------------------- Mouse
# [5] ####################...#################### Rat
# [6] ####################...#################### Dog
# [7] ####################...#################### Chicken
# [8] ####################...#################### Salmon


## append="union"
rowmask(maskTest,append="union") <- IRanges(start=7,end=8) 
maskTest
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] -----TCCCGTCTCCGCAGC...ATTAAAAAAAAAAAAAAAAA Human
# [2] --------------------...-------------------- Chimp
# [3] --------------------...-------------------- Cow
# [4] --------------------...-------------------- Mouse
# [5] ####################...#################### Rat
# [6] ####################...#################### Dog
# [7] ####################...#################### Chicken
# [8] ####################...#################### Salmon
```

函数 `maskMotif()` 也可以对 `MultipleAlignment` 对象进行操作，用于在列的水平上mask相同序列的出现。

```R
tataMasked <- maskMotif(origMAlign, "TATA") 
colmask(tataMasked)
# NormalIRanges object with 5 ranges and 0 metadata columns:
#           start       end     width
#       <integer> <integer> <integer>
#   [1]       811       814         4
#   [2]      1180      1183         4
#   [3]      1186      1191         6
#   [4]      1204      1207         4
#   [5]      1218      1221         4
```

函数 `maskGaps()` 可以基于设定的参数在列的水平上进行mask.

> **min.fraction** A value in [0,1] that indicates the minimum fraction needed to call a gap in
> the consensus string (default is 0.5).
> **min.block.width** A positive integer that indicates the minimum number of consecutive gaps
> to mask, as defined by min.fraction (default is 4).

```R
autoMasked <- maskGaps(origMAlign, min.fraction=0.5, min.block.width=4) 
autoMasked
# DNAMultipleAlignment with 8 rows and 2343 columns
#      aln                                         names               
# [1] ####################...#################### Human
# [2] ####################...#################### Chimp
# [3] ####################...#################### Cow
# [4] ####################...#################### Mouse
# [5] ####################...#################### Rat
# [6] ####################...#################### Dog
# [7] ####################...#################### Chicken
# [8] ####################...#################### Salmon
```

`as.matrix()` 可以应用在 `MultipleAlignment` 对象上，将其转换为矩阵。

```R
full = as.matrix(origMAlign) 
dim(full)
# [1]    8 2343
partial = as.matrix(autoMasked) 
dim(partial)
# [1]    8 1143
```

## 3. 分析应用

### 3.1 完成mask的序列的碱基频率

```R
alphabetFrequency(autoMasked)
#        A   C   G   T M R W S Y K V H D B N   - + .
# [1,] 260 351 296 218 0 0 0 0 0 0 0 0 0 0 0  18 0 0
# [2,] 171 271 231 128 0 0 0 0 0 0 0 0 0 0 3 339 0 0
# [3,] 277 360 275 209 0 0 0 0 0 0 0 0 0 0 0  22 0 0
# [4,] 265 343 277 226 0 0 0 0 0 0 0 0 0 0 0  32 0 0
# [5,] 251 345 287 229 0 0 0 0 0 0 0 0 0 0 0  31 0 0
# [6,] 160 285 241 118 0 0 0 0 0 0 0 0 0 0 0 339 0 0
# [7,] 224 342 273 190 0 0 0 0 0 0 0 0 0 0 0 114 0 0
# [8,] 268 289 273 262 0 0 0 0 0 0 0 0 0 0 0  51 0 0
```

### 3.2 计算一致性矩阵 (consensus matrix)

```R
consensusMatrix(autoMasked, baseOnly=TRUE)[, 1138:1143] 
#       [,1] [,2] [,3] [,4] [,5] [,6]
# A        0    1    0    0    1    0
# C        5    4    4    0    1    1
# G        1    1    1    0    0    1
# T        0    0    1    6    4    4
# other    2    2    2    2    2    2
```

### 3.3 提取一致性序列 (consensus string) & Consensus Views

```R
substr(consensusString(autoMasked),80,125) 
# [1] "####CRGABAMGTCA-YRGCTTCTCYGTSCAWAGGCRRTGRCYTGT"
consensusViews(autoMasked) 
#   Views on a 2343-letter BString subject
# subject: ----------------------VWVMKYY...----------------------------
# views:
#     start  end width
# [1]    84  325   242 [CRGABAMGTCA-YRGCTTCTCY...TCSGCYGGSGCYRYCCTGSGG]
# [2]   330  332     3 [CCR]
# [3]   338 1191   854 [CTGCTGCTGYCGGGVCACGGCG...GTTTTTATGTATAAATATATA]
# [4]  1198 1241    44 [ATAAAATATAAKAC--TTTTTATAYRSCARATGTAAAAATTCAA]
```

### 3.4 聚类分析&树形图

用masking前的序列：

```R
sdist <- stringDist(as(origMAlign,"DNAStringSet"), method="hamming") 
clust <- hclust(sdist, method = "single") 
plot(clust)
```

嗯，得到了一张hin奇怪的图。

![](https://upload-images.jianshu.io/upload_images/14383117-fe70878d98c4c87d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



如果用masking后的序列：

```R
sdist <- stringDist(as(autoMasked,"DNAStringSet"), method="hamming") 
clust <- hclust(sdist, method = "single")
plot(clust) 
```

改善，但依旧——

![](https://upload-images.jianshu.io/upload_images/14383117-40cfcc05dcbba03c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然小鼠、大鼠和人类的高相似性是因为这三个物种的基因组序列整体更长，而第一张图里的奇怪关系是因为 rodent/mouse/human 之间的 extra "length", 而不是保守区域的相似性。

## 4. 导出文件

将 `MultipleAlignment` 对象转换为 `DNAStringSet` ，导出为 fasta 文件。

```R
DNAStr = as(origMAlign, "DNAStringSet")
writeXStringSet(DNAStr, file="myFile.fa") 
```

![](https://upload-images.jianshu.io/upload_images/14383117-7fb1bdc6e56ec1e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## References

- **MultipleAlignment Objects** https://bioconductor.org/packages/release/bioc/vignettes/Biostrings/inst/doc/MultipleAlignments.pdf

- Nsofor C A, Ogbulie T E, Ugbogu O, et al. **Whole Genome Sequencing: Bacterial Typing Revolutionized**[J]. Journal of Microbiology and Biotechnology Research, 2015, 1(3): 38-48.
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)


   
