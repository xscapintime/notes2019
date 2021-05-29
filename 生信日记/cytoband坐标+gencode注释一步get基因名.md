> ![](https://upload-images.jianshu.io/upload_images/14383117-1aabca04d05fd513.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
想去这样的实验室🤐，图源水印


需要下载两个文件：

[cytoband文件](http://hgdownload.soe.ucsc.edu/goldenPath/hg38/database/cytoBand.txt.gz) 

[gencode注释文件](ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_31/gencode.v31.annotation.gtf.gz)

用到的包：

```R
if(!require(GenomicRanges)){
  BiocManager::install("GenomicRanges")
  library(GenomicRanges)
}
```
文件下载到本地后，简单粗暴读取

```R
cytobandfile <- read.csv("cytoBand.txt.gz", sep = "\t",header = F)
gencodeinfo <- read.csv("gencode_v29_human_gene_info",sep = "\t",header = F)
```

先对 `cytobandfile` 做些文本处理

![](https://upload-images.jianshu.io/upload_images/14383117-23934ff470980d43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
cytobandfile$band <- gsub("[[:alpha:]]","",cytobandfile$V4)
cytobandfile$arm <- gsub("\\d.+","",cytobandfile$V4)
colnames(cytobandfile) <- c("chr","start","end", "stain","band","arm")
cytobandfile <- cytobandfile[,-4]  ##其实不把 p,q 和数字分开似乎更方便
cytobandfile$gene_name <- NA
```

![](https://upload-images.jianshu.io/upload_images/14383117-b925413f7982944b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`gencodeinfo` 输入正确列名

```R
colnames(gencodeinfo) <- c("gene_name", "gene_type", "gene_id",
                           "seqnames", "start", "end")
```

一步get时间到：

```R
Chrloc2Sym <- function(cytobandfile,gencodeinfo,arm,band) {
  ## 构建cytoband GRanges
  cytobandgr <-  GRanges(seqnames = Rle(cytobandfile$chr),
                         ranges = IRanges(start = cytobandfile$start, 
                                          end = cytobandfile$end),
                         stain = cytobandfile$stain, 
                         arm = cytobandfile$arm,
                         band = cytobandfile$band,
                         symbol = cytobandfile$gene_name)
  ## 构建gencodeinfo GRanges
  gencodegr <- GRanges(seqnames = Rle(gencodeinfo$seqnames),
                       ranges = IRanges(start = gencodeinfo$start, 
                                        end = gencodeinfo$end),
                       type = gencodeinfo$gene_type,
                       ensemble = gencodeinfo$gene_id,
                       symbol = gencodeinfo$gene_name)
  ## overlap subsetting
  subs <- subsetByOverlaps(cytobandgr,gencodegr)
  ## get the index
  ovlporder <- match(subs@ranges,cytobandgr@ranges)
  ## merge the symbol
  subs@elementMetadata@listData[["symbol"]] <- gencodegr@elementMetadata@listData[["symbol"]][ovlporder]
  ## band&arm to symbol
  subs[subs@elementMetadata@listData[["arm"]]==arm & subs@elementMetadata@listData[["band"]]==band,"symbol"]
}
```

试试水：

```R
Chrloc2Sym(cytobandfile = cytobandfile, gencodeinfo = gencodeinfo,arm = "p", band = "36.21")
# GRanges object with 1 range and 1 metadata column:
#       seqnames            ranges strand |   symbol
#          <Rle>         <IRanges>  <Rle> | <factor>
#   [1]     chr1 12500000-15900000      * |  OR4G11P
#   -------
#   seqinfo: 595 sequences from an unspecified genome; no seqlengths
```

完美┏ (゜ω゜)=☞

## References
🈚

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)


