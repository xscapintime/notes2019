> 😑
![](https://upload-images.jianshu.io/upload_images/14383117-39906d230e452e5a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 导入及检索 Gene Model 的便捷工具

> Lawrence M, Huber W, Pagès H, Aboyoun P, Carlson M, Gentleman R, Morgan M, Carey V (2013). “Software for Computing and Annotating Genomic Ranges.” *PLoS Computational Biology*, **9**. doi: [10.1371/journal.pcbi.1003118](https://doi.org/10.1371/journal.pcbi.1003118), 

> *A **Gene Model** is defined as any description of a gene product from a variety of sources including computational prediction, mRNA sequencing, or genetic characterization.*

## 1. 关于 `GenomicFeatures`

`GenomicFeatures` 可以从 UCSC Genome Bioinformatics 及 BioMart 等数据库检索并获取转录本相关信息并对其进行操作，有助于 ChIP-chip, ChIP-seq, RNA-seq 结果分析。

`GenomicFeatures` 利用 `TxDb` 对象储存转录本元数据，包括 5', 3' UTRs, CDS, 外显子等。同时 `TxDb` 对象有大量访问函数可获取这些信息。

```R
library(GenomicFeatures)
ls("package:GenomicFeatures")
# [1] "as.list"                      "asBED"                       
# [3] "asGFF"                        "browseUCSCtrack"             
# [5] "cds"                          "cdsBy"                       
# [7] "cdsByOverlaps"                "coverageByTranscript"        
# [9] "DEFAULT_CIRC_SEQS"            "disjointExons"               
# [11] "distance"                     "exonicParts"                 
# [13] "exons"                        "exonsBy"                     
# [15] "exonsByOverlaps"              "extractTranscriptSeqs"       
# [17] "extractUpstreamSeqs"          "features"                    
# [19] "fiveUTRsByTranscript"         "genes"                       
# [21] "getChromInfoFromBiomart"      "getChromInfoFromUCSC"        
# [23] "getPromoterSeq"               "id2name"                     
# [25] "intronicParts"                "intronsByTranscript"         
# [27] "isActiveSeq"                  "isActiveSeq<-"               
# [29] "makeFDbPackageFromUCSC"       "makeFeatureDbFromUCSC"       
# [31] "makePackageName"              "makeTxDb"                    
# [33] "makeTxDbFromBiomart"          "makeTxDbFromEnsembl"         
# [35] "makeTxDbFromGFF"              "makeTxDbFromGRanges"         
# [37] "makeTxDbFromUCSC"             "makeTxDbPackage"             
# [39] "makeTxDbPackageFromBiomart"   "makeTxDbPackageFromUCSC"     
# [41] "mapFromTranscripts"           "mapIdsToRanges"              
# [43] "mapRangesToIds"               "mapToTranscripts"            
# [45] "microRNAs"                    "organism"                    
# [47] "pcoverageByTranscript"        "pmapFromTranscripts"         
# [49] "pmapToTranscripts"            "promoters"                   
# [51] "seqinfo"                      "seqlevels<-"                 
# [53] "seqlevels0"                   "show"                        
# [55] "species"                      "supportedMiRBaseBuildValues" 
# [57] "supportedUCSCFeatureDbTables" "supportedUCSCFeatureDbTracks"
# [59] "supportedUCSCtables"          "threeUTRsByTranscript"       
# [61] "tidyExons"                    "tidyIntrons"                 
# [63] "tidyTranscripts"              "transcriptLengths"           
# [65] "transcriptLocs2refLocs"       "transcripts"                 
# [67] "transcriptsBy"                "transcriptsByOverlaps"       
# [69] "transcriptWidths"             "tRNAs"                       
# [71] "UCSCFeatureDbTableSchema"    
```

## 2. 从 `TxDb` 获取数据

### 2.1 加载转录本数据

- `loadDb()`
- 直接加载 TxDb 注释包

```R
samplefile <- system.file("extdata", "hg19_knownGene_sample.sqlite", 
                          package="GenomicFeatures") 
txdb_1 <- loadDb(samplefile) 
txdb_1 
# TxDb object:
# # Db type: TxDb
# # Supporting package: GenomicFeatures
# # Data source: UCSC
# # Genome: hg19
# # Organism: Homo sapiens
# # UCSC Table: knownGene
# # Resource URL: http://genome.ucsc.edu/
# # Type of Gene ID: Entrez Gene ID
# # Full dataset: no
# # miRBase build ID: NA
# # transcript_nrow: 178
# # exon_nrow: 620
# # cds_nrow: 523
# # Db created by: GenomicFeatures package from Bioconductor
# # Creation time: 2014-10-08 10:31:15 -0700 (Wed, 08 Oct 2014)
# # GenomicFeatures version at creation time: 1.17.21
# # RSQLite version at creation time: 0.11.4
# # DBSCHEMAVERSION: 1.0
```

```R
library(TxDb.Hsapiens.UCSC.hg19.knownGene) 
txdb <- TxDb.Hsapiens.UCSC.hg19.knownGene  
txdb 
# TxDb object:
# # Db type: TxDb
# # Supporting package: GenomicFeatures
# # Data source: UCSC
# # Genome: hg19
# # Organism: Homo sapiens
# # Taxonomy ID: 9606
# # UCSC Table: knownGene
# # Resource URL: http://genome.ucsc.edu/
# # Type of Gene ID: Entrez Gene ID
# # Full dataset: yes
# # miRBase build ID: GRCh37
# # transcript_nrow: 82960
# # exon_nrow: 289969
# # cds_nrow: 237533
# # Db created by: GenomicFeatures package from Bioconductor
# # Creation time: 2015-10-07 18:11:28 +0000 (Wed, 07 Oct 2015)
# # GenomicFeatures version at creation time: 1.21.30
# # RSQLite version at creation time: 1.0.0
# # DBSCHEMAVERSION: 1.1
```
### 2.2  基于染色体的数据预筛选

`seqlevels()` 查看染色体：

```R
head(seqlevels(txdb))
# [1] "chr1" "chr2" "chr3" "chr4" "chr5" "chr6"
```

通过赋值选择染色体：

```R
seqlevels(txdb) <- "chr1" 
```

如果需要设置回全部染色体的状态，使用函数 `seqlevels0()`：

```R
seqlevels(txdb) <- seqlevels0(txdb) 
```

### 2.3 数据筛选

`AnnotationDbi` 包内的 `columns()` , `keytypes()` , `keys()` , `select()` 同样适用于 `TxDb` 对象。

```R
## GENEID → TXNAME
keys <- c("100033416", "100033417", "100033420") 
columns(txdb) 
#  [1] "CDSCHROM"   "CDSEND"     "CDSID"      "CDSNAME"   
#  [5] "CDSSTART"   "CDSSTRAND"  "EXONCHROM"  "EXONEND"   
#  [9] "EXONID"     "EXONNAME"   "EXONRANK"   "EXONSTART" 
# [13] "EXONSTRAND" "GENEID"     "TXCHROM"    "TXEND"     
# [17] "TXID"       "TXNAME"     "TXSTART"    "TXSTRAND"  
# [21] "TXTYPE"    
keytypes(txdb) 
# [1] "CDSID"    "CDSNAME"  "EXONID"   "EXONNAME" "GENEID"  
# [6] "TXID"     "TXNAME" 
select(txdb,
       keys = keys, 
       columns = "TXNAME",
       keytype = "GENEID") 
# 'select()' returned 1:1 mapping between keys and columns
#      GENEID     TXNAME
# 1 100033416 uc001yxl.4
# 2 100033417 uc001yxo.3
# 3 100033420 uc001yxr.3
```

```R
## GENEID → TXNAME + TXTSTRAND + TXCHROM
cols <- c("TXNAME", "TXSTRAND", "TXCHROM") 
select(txdb, 
       keys = keys, 
       columns = cols, 
       keytype = "GENEID") 
# 'select()' returned 1:1 mapping between keys and columns
#      GENEID     TXNAME TXCHROM TXSTRAND
# 1 100033416 uc001yxl.4   chr15        +
# 2 100033417 uc001yxo.3   chr15        +
# 3 100033420 uc001yxr.3   chr15        +
```

## 3. 主要函数及示例

> **Extract basic quantities**
>
> - `genes()`
> - `transcripts()`
> - `cds()`
> - `exons()`
> - `microRNAs()`
> - `tRNAs()`
> - `promoters()`
>
> **Extract quantities and group**
>
> - `transcriptsBy(by = c("gene", "exon", "cds"))`
> - `cdsBy(by = c("tx", "gene"))`
> - `exonsBy(by = c("tx", "gene"))`
> - `intronsByTranscript()`
> - `fiveUTRsByTranscript()`
> - `threeUTRsByTranscript()`
>
> (Note: there are grouping functions without the non-grouping function and vice versa; there is for example no `introns()` function.
>
> **Other functions**
>
> - `transcriptLengths()` (optionally include CDS length etc).
> - `XXByOverlaps()` (select features based on overlaps with `XX` being `transcript`, `cds` or `exon`).
>
> **Mapping between genome and transcript coordinates**
>
> - `extractTranscriptSeqs()` (getting RNA sequencing of the transcripts).

### 3.1 返回 `GRanges` 对象

除了**2.3** 的数据筛选方法，直接提取出 `GRanges` 对象的方法可能更方便。**"Extract basic quantities"** 下的所有函数都可返回含有基因组坐标、外显子区间、转录本、编码序列等信息的 `GRanges` 对象。

#### 3.1.1 `genes()`

```R
GE <- genes(txdb)
# [1] "GRanges"
# attr(,"package")
# [1] "GenomicRanges"
GE[1:5]
# GRanges object with 5 ranges and 1 metadata column:
#         seqnames              ranges strand |     gene_id
#            <Rle>           <IRanges>  <Rle> | <character>
#       1    chr19   58858172-58874214      - |           1
#      10     chr8   18248755-18258723      + |          10
#     100    chr20   43248163-43280376      - |         100
#    1000    chr18   25530930-25757445      - |        1000
#   10000     chr1 243651535-244006886      - |       10000
#   -------
#   seqinfo: 93 sequences (1 circular) from hg19 genome
```

#### 3.1.2 `transcripts()`

```R
TC <- transcripts(txdb) 
TC[1:3]
# GRanges object with 3 ranges and 2 metadata columns:
#       seqnames      ranges strand |     tx_id     tx_name
#          <Rle>   <IRanges>  <Rle> | <integer> <character>
#   [1]     chr1 11874-14409      + |         1  uc001aaa.3
#   [2]     chr1 11874-14409      + |         2  uc010nxq.1
#   [3]     chr1 11874-14409      + |         3  uc010nxr.1
#   -------
#   seqinfo: 93 sequences (1 circular) from hg19 genome
```

```R
## strand information
strand(TC)
# factor-Rle of length 82960 with 94 runs
#   Lengths: 4073 3894 2642 2450 2179 ...    2    1    2    2    1
#   Values :    +    -    +    -    + ...    -    +    -    +    -
# Levels(3): + - *
length(TC)
# 82960
```

用 `filter=` 进一步筛选：

```R
TC_select <- transcripts(txdb,filter = list(tx_chrom = "chr15",
                                        tx_strand = "+"))
length(TC_select)
# [1] 1732
```

#### 3.1.3 `promoters()` 

```R
PR <- promoters(txdb, upstream = 2000, downstream = 400) 
PR[1:3]
# GRanges object with 3 ranges and 2 metadata columns:
#              seqnames     ranges strand |     tx_id     tx_name
#                 <Rle>  <IRanges>  <Rle> | <integer> <character>
#   uc001aaa.3     chr1 9874-12273      + |         1  uc001aaa.3
#   uc010nxq.1     chr1 9874-12273      + |         2  uc010nxq.1
#   uc010nxr.1     chr1 9874-12273      + |         3  uc010nxr.1
#   -------
#   seqinfo: 93 sequences (1 circular) from hg19 genome
```

#### 3.1.4 另一种方法

```R
gr <- GRanges(seqnames = "chr1", 
              strand = "+", 
              ranges = IRanges(start = 11874, end = 14409))
subsetByOverlaps(exons(txdb), gr)
# GRanges object with 6 ranges and 1 metadata column:
#       seqnames      ranges strand |   exon_id
#          <Rle>   <IRanges>  <Rle> | <integer>
#   [1]     chr1 11874-12227      + |         1
#   [2]     chr1 12595-12721      + |         2
#   [3]     chr1 12613-12721      + |         3
#   [4]     chr1 12646-12697      + |         4
#   [5]     chr1 13221-14409      + |         5
#   [6]     chr1 13403-14409      + |         6
#   -------
#   seqinfo: 93 sequences (1 circular) from hg19 genome
```

当然中间的 `exons()` 可以是任意一个**"Extract basic quantities"** 下的函数。

### 3.2 特征分组

**Extract quantities and group** 下的函数可返回 `GRangesList` 对象，每个函数都有特定的 `by=` 参数选项。

```R
TC_bygene <- transcriptsBy(txdb, by = "gene") 
class(TC_bygene)
# [1] "CompressedGRangesList"
# attr(,"package")
# [1] "GenomicRanges"
length(TC_bygene)
# [1] 23459
names(TC_bygene)[10:13]
# [1] "10003"     "100033413" "100033414" "100033415"
```

```R
TC_bycds <- transcriptsBy(txdb, by = "cds")
length(TC_bycds)
# [1] 237533
names(TC_bycds)[10:13]
# [1] "10" "11" "12" "13"
TC_bycds[1:3]
# GRangesList object of length 3:
# $1 
# GRanges object with 1 range and 3 metadata columns:
#       seqnames      ranges strand |     tx_id     tx_name exon_rank
#          <Rle>   <IRanges>  <Rle> | <integer> <character> <integer>
#   [1]     chr1 11874-14409      + |         2  uc010nxq.1         1
# 
# $2 
# GRanges object with 1 range and 3 metadata columns:
#       seqnames      ranges strand | tx_id    tx_name exon_rank
#   [1]     chr1 11874-14409      + |     2 uc010nxq.1         2
# 
# $3 
# GRanges object with 1 range and 3 metadata columns:
#       seqnames      ranges strand | tx_id    tx_name exon_rank
#   [1]     chr1 11874-14409      + |     2 uc010nxq.1         3
# 
# -------
# seqinfo: 93 sequences (1 circular) from hg19 genome
```

同样的，另一种方法：

```R
gr <- GRanges(seqnames = "chr1", 
              strand = "+", 
              ranges = IRanges(start = 11874, end = 14409))
subsetByOverlaps(cdsBy(txdb, by = "tx"), gr)
# GRangesList object of length 1:
# $2 
# GRanges object with 3 ranges and 3 metadata columns:
#       seqnames      ranges strand |    cds_id    cds_name exon_rank
#          <Rle>   <IRanges>  <Rle> | <integer> <character> <integer>
#   [1]     chr1 12190-12227      + |         1        <NA>         1
#   [2]     chr1 12595-12721      + |         2        <NA>         2
#   [3]     chr1 13403-13639      + |         3        <NA>         3
# 
# -------
# seqinfo: 93 sequences (1 circular) from hg19 genome
```

### 3.3 一步获得非编码区及内含子的长度

`intronsByTranscript()` , `fiveUTRsByTranscript()` , `threeUTRsByTranscript()` 是分组函数的“预先设定”版。

```R
intronsByTranscript(txdb)
# GRangesList object of length 82960:
# $1 
# GRanges object with 2 ranges and 0 metadata columns:
#       seqnames      ranges strand
#          <Rle>   <IRanges>  <Rle>
#   [1]     chr1 12228-12612      +
#   [2]     chr1 12722-13220      +
# 
# $2 
# GRanges object with 2 ranges and 0 metadata columns:
#       seqnames      ranges strand
#   [1]     chr1 12228-12594      +
#   [2]     chr1 12722-13402      +
# 
# $3 
# GRanges object with 2 ranges and 0 metadata columns:
#       seqnames      ranges strand
#   [1]     chr1 12228-12645      +
#   [2]     chr1 12698-13220      +
# 
# ...
# <82957 more elements>
# -------
# seqinfo: 93 sequences (1 circular) from hg19 genome
```

```R
length(intronsByTranscript(txdb))
# [1] 82960
length(fiveUTRsByTranscript(txdb))
# [1] 61495
length(threeUTRsByTranscript(txdb))
# [1] 60740
```

## 4. 获取序列

当和合适的 `Bsgenome` 包配合使用，`GenomicFeatures` 也可以直接获取到序列数据。

```R
library(BSgenome.Hsapiens.UCSC.hg19) 
```
获取转录本序列：

```R
tx_seqs1 <- extractTranscriptSeqs(Hsapiens, 
                                  TxDb.Hsapiens.UCSC.hg19.knownGene, 
                                  use.names=TRUE) 
tx_seqs1
#   A DNAStringSet instance of length 82960
#          width seq                                  names               
#     [1]   1652 CTTGCCGTCAGCCTTTT...ACACTGTTGGTTTCTG uc001aaa.3
#     [2]   1488 CTTGCCGTCAGCCTTTT...ACACTGTTGGTTTCTG uc010nxq.1
#     [3]   1595 CTTGCCGTCAGCCTTTT...ACACTGTTGGTTTCTG uc010nxr.1
#     [4]    918 ATGGTGACTGAATTCAT...TAGTGTAAAGTTTTAG uc001aal.1
#     [5]     32 TACAGACCAAGCTCATGACTCACAATGGCCTA     uc001aaq.2
#     ...    ... ...
# [82956]   1217 GCCAGTTTAGGGTCTCT...TCCGCCTCCTGAGATC uc011mgu.1
# [82957]    737 CTCCACTTCTGATCCTC...TTTATTAGATGCAGTG uc011mgv.2
# [82958]     30 TGGTGAATTTCAGCCAAAGTGGCCAAAGAA       uc011mgw.1
# [82959]     30 TTGCAGAGGTGGCTGGTTGCTCTTTGAGCC       uc022brq.1
# [82960]     30 TGGTGAATTTCAGCCAAAGTGGCCAAAGAA       uc022brr.1

```

还可以翻译：

```R
translate(tx_seqs1)
#  A AAStringSet instance of length 82960
#         width seq                                   names               
#     [1]   550 LAVSLFFDLFFLFMCIC...LHPAQLEILY*KHTVGF uc001aaa.3
#     [2]   496 LAVSLFFDLFFLFMCIC...ASCTARDPLLKAHCWFL uc010nxq.1
#     [3]   531 LAVSLFFDLFFLFMCIC...LHPAQLEILY*KHTVGF uc010nxr.1
#     [4]   306 MVTEFIFLGLSDSQELQ...AIRQLRKWDAHSSVKF* uc001aal.1
#     [5]    10 YRPSS*LTMA                            uc001aaq.2
#     ...   ... ...
# [82956]   405 ASLGSLVSPAELLCSRL...LRRASMATDHCNLRLLR uc011mgu.1
# [82957]   245 LHF*SSPPWCCSGSHTR...KWENGFLGLK*PLY*MQ uc011mgv.2
# [82958]    10 W*ISAKVAKE                            uc011mgw.1
# [82959]    10 MQRWLVAL*A                            uc022brq.1
# [82960]    10 W*ISAKVAKE                            uc022brr.1
```
























## References

- **Making and Utilizing TxDb Objects** https://bioconductor.org/packages/release/bioc/vignettes/GenomicFeatures/inst/doc/GenomicFeatures.pdf

- **GenomicFeatures Man Pages** <https://kasperdanielhansen.github.io/genbioconductor/html/GenomicFeatures.html#examples>

- **Using the Gene Search Results** <https://www.arabidopsis.org/help/helppages/generesu.jsp>
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
