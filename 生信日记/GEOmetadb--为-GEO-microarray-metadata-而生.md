> 发现了一个有趣的大佬
![](https://upload-images.jianshu.io/upload_images/14383117-d465ba9f121cb54b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## A compilation of metadata from NCBI GEO

> Zhu Y, Davis S, Stephens R, Meltzer PS, Chen Y (2008). “GEOmetadb: powerful alternative search engine for the Gene Expression Omnibus.” *Bioinformatics (Oxford, England)*, **24**(23), 2798–2800. ISSN 1367-4811, doi: [10.1093/bioinformatics/btn520](https://doi.org/10.1093/bioinformatics/btn520), <http://www.ncbi.nlm.nih.gov/pubmed/18842599>.

## 1. 关于 `GEOmetadb`

GEO (Gene Expression Omnibus) 众所周知就是 NCBI 里的那个 GEO , 全称基因表达~~公共汽车~~汇编，里面有很多有用的东西。但似乎界面不是很友好，挖到一个歪果友人2010年的上古吐槽:

> "At work, we joke that GEO is the only database where data goes in, but it won’t come out. "

于是 `GEOmetadb` 的出现就非常酷炫，不但能得到 microarray 数据，还能 get 元数据 (metadata) .

## 2. 开始上手

包内并没有预装数据库，所以第一次使用需要先下载数据库，之后也可以通过同样的方式重新下载，这样的优点是可以保证每次使用的都是最新版本的数据库。

```R
library(GEOmetadb)
if(!file.exists('GEOmetadb.sqlite')) getSQLiteFile() ## 更新数据库直接 getSQLiteFile() 即可
# trying URL 'http://starbuck1.s3.amazonaws.com/sradb/GEOmetadb.sqlite.gz'
# Content type 'binary/octet-stream' length 528141472 bytes (503.7 MB)
# downloaded 4.3 MB
# 
# Unzipping...
# Error in result_create(conn@ptr, statement) : 
#   database disk image is malformed
# In addition: Warning messages:
# 1: In download.file(url_geo, destfile = localfile, mode = "wb") :
#   downloaded length 4509696 != reported length 528141472
# 2: Couldn't set synchronous mode: database disk image is malformed
# Use `synchronous` = NULL to turn off this warning. 
```

果然在国内用这种方法下载是注定失败的，直接下载文件 ‘http://starbuck1.s3.amazonaws.com/sradb/GEOmetadb.sqlite.gz’ 到工作目录亲测有效。

```R
## 查看数据库信息
file.info('GEOmetadb.sqlite')
#                       size isdir mode               mtime               ctime
# GEOmetadb.sqlite 8811945984 FALSE  666 2019-06-17 21:03:05 2019-06-19 21:23:24
#                                atime exe
# GEOmetadb.sqlite 2019-06-19 21:23:24  no

## 建立连接
con <- dbConnect(SQLite(), 'GEOmetadb.sqlite')

## 关闭连接
dbDisconnect(con)
```

## 3. 一些例子

### 3.1 连接数据库

于是再连一下：

```R
con <- dbConnect(SQLite(), 'GEOmetadb.sqlite')
```

`dbListTables()` 查看所有的 table：

```R
geo_tables <- dbListTables(con)
geo_tables
#  [1] "gds"               "gds_subset"        "geoConvert"       
#  [4] "geodb_column_desc" "gpl"               "gse"             
#  [7] "gse_gpl"           "gse_gsm"           "gsm"             
# [10] "metaInfo"          "sMatrix"    
```

`dbListFields()` 查看某个 table 下的数据库字段/栏位 (database field)：

```R
dbListFields(con,'gse')
#  [1] "ID"                   "title"                "gse"                 
#  [4] "status"               "submission_date"      "last_update_date"    
#  [7] "pubmed_id"            "summary"              "type"                
# [10] "contributor"          "web_link"             "overall_design"      
# [13] "repeats"              "repeats_sample_list"  "variable"            
# [16] "variable_description" "contact"              "supplementary_file"  
```

查看某个 table 的 SQL schema:

> A schema in a SQL database is a collection of logical structures of data. 

```R
dbGetQuery(con,'PRAGMA TABLE_INFO(gpl)')
#    cid                 name type notnull dflt_value pk
# 1    0                   ID REAL       0         NA  0
# 2    1                title TEXT       0         NA  0
# 3    2                  gpl TEXT       0         NA  0
# 4    3               status TEXT       0         NA  0
# 5    4      submission_date TEXT       0         NA  0
# 6    5     last_update_date TEXT       0         NA  0
# 7    6           technology TEXT       0         NA  0
# 8    7         distribution TEXT       0         NA  0
# 9    8             organism TEXT       0         NA  0
# 10   9         manufacturer TEXT       0         NA  0
# 11  10 manufacture_protocol TEXT       0         NA  0
# 12  11              coating TEXT       0         NA  0
# 13  12       catalog_number TEXT       0         NA  0
# 14  13              support TEXT       0         NA  0
# 15  14          description TEXT       0         NA  0
# 16  15             web_link TEXT       0         NA  0
# 17  16              contact TEXT       0         NA  0
# 18  17       data_row_count REAL       0         NA  0
# 19  18   supplementary_file TEXT       0         NA  0
# 20  19         bioc_package TEXT       0         NA  0
```

### 3.2 写 SQL 询问语句 & 获取信息

#### 3.2.1 简单的询问和统计

```R
## count samples per GDS
gds.count <- dbGetQuery(con, "select gds,sample_count from gds")
gds.count[1:5,]
#     gds sample_count
# 1  GDS5            5
# 2  GDS6           29
# 3 GDS10           28
# 4 GDS12            8
# 5 GDS15            6
```

```R
## count samples per GSE
gse <- dbGetQuery(con, "select series_id from gsm")
## The gsm table contains GSM sample accession and GSE series accession (in the series_id field)
gse.count <- as.data.frame(table(gse$series_id))
gse.count[1:10,]
#                          Var1 Freq
# 1                        GSE1   38
# 2                       GSE10    4
# 3                      GSE100    4
# 4  GSE10000,GSE21419,GSE40156   15
# 5           GSE10000,GSE40156   14
# 6                   GSE100001    6
# 7         GSE100002,GSE100005    4
# 8                   GSE100003    6
# 9         GSE100004,GSE100005   30
# 10                  GSE100006   18
```

#### 3.2.2 从 *gse* 这个 table 拿到5条记录

```R
rs_1 <- dbGetQuery(con,'select * from gse limit 5')
dim(rs_1)
# [1]  5 18
rs_1[,1:5]
#   ID                                                    title  gse
# 1  1                                     NHGRI_Melanoma_class GSE1
# 2  2                                   Cerebellar development GSE2
# 3  3             Renal Cell Carcinoma Differential Expression GSE3
# 4  4     Diurnal and Circadian-Regulated Genes in Arabidopsis GSE4
# 5  5 Global profile of germline gene expression in C. elegans GSE5
#                  status submission_date
# 1 Public on Jan 22 2001      2001-01-22
# 2 Public on Apr 26 2001      2001-04-19
# 3 Public on Jul 19 2001      2001-07-19
# 4 Public on Jul 20 2001      2001-07-20
# 5 Public on Jul 24 2001      2001-07-24
```

#### 3.2.3 获取所有由 "Sean Davis" 提交的 GEO accession 和文章标题：

```R
rs_2 <- dbGetQuery(con, paste("select gse, title from gse where",
                              "contributor like '%Sean%Davis%'",
                              sep = " "))  ## "like"和"%"结合作为通配符
rs_2
#         gse
# 1   GSE2553
# 2   GSE4406
# 3   GSE5357
# 4   GSE7376
# 5   GSE7882
# 6   GSE8486
# 7   GSE9328
# 8  GSE14543
# 9  GSE15621
# 10 GSE16087
# 11 GSE16088
# 12 GSE16091
# 13 GSE16102
# 14 GSE18544
# 15 GSE19063
# 16 GSE20016
# 17 GSE22520
# 18 GSE25127
# 19 GSE25164
# 20 GSE29428
# 21 GSE41795
# 22  GSE5481
# 23 GSE33249
# 24 GSE52137
# 25 GSE80005
#                                                                                                                      title
# 1                                                                                                      NHGRI_Sarcoma_Baird
# 2                                           Gene expression profiling of CD4+ T-cells and GM6990 lymphoblastoid cell lines
# 3                                                                                                    NHGRI Menin ChIP-Chip
# 4                                                                Detection of novel amplification units in prostate cancer
# 5                          Gene Expression and Comparative Genomic Hybridization of Ductal Carcinoma In Situ of the Breast
# 6                                                                Whole genome DNAse hypersensitivity in human CD4+ T-cells
# 7                                                                                              ATF2 knockout in papillomas
# 8                                                                              A molecular function map of Ewing’s Sarcoma
# 9                                                                  Acute Lymphocytic Leukemia versus associated xenografts
# 10                                                                         Gene expression profiles of canine osteosarcoma
# 11                                                                          Gene expression profiles of human osteosarcoma
# 12                                                                    Gene expression profiles of human osteosarcoma, set2
# 13                                                               Gene expression profiles of canine and human osteosarcoma
# 14     Expression Profiling of a Mouse Xenograft Model of “Triple-Negative” Breast Cancer Brain Metastases With Vorinostat
# 15                                                          Genome-wide map of PAX3-FKHR binding sites in rhabdomyosarcoma
# 16 Analyses of Human Brain Metastases of Breast Cancer Reveal the Association between HK2 Up-Regulation and Poor Prognosis
# 17                                             Mouse Models of Alveolar/Embryonal Rhabdomyosarcoma & Spindle Cell Sarcomas
# 18                                                                       Ewing Sarcoma cell lines treated with mithramycin
# 19                                                                                         UV effects in mouse melanocytes
# 20       A methyl-deviator epigenotype of estrogen-receptor-positive breast carcinoma is associated with malignant biology
# 21                               Accelerated high-yield generation of limb-innervating motor neurons from human stem cells
# 22                                                                                             BRAF siRNA NHGRI_Liang_BRAF
# 23                    High resolution comparative genomic hybridisation analysis of ductal carcinoma in situ of the breast
# 24                                            Expression data from thymic epithelial cells of 5-6 weeks old C57BL/6J mice.
# 25                                                     Gene expression profile in mouse esophageal squamous cell carcinoma
```

> The LIKE operator is used in a WHERE clause to search for a specified pattern in a column.
>
> There are two wildcards often used in conjunction with the LIKE operator:
>
> - % - The percent sign represents zero, one, or multiple characters
> - _ - The underscore represents a single character

#### 3.2.4 找到所有 GPL96 (Affymetrix hgu133a) 样本及 .CEL 文件下载链接

```R
rs_3 <- dbGetQuery(con, paste("select gsm, supplementary_file",
                              "from gsm where gpl = 'GPL96'",
                              "and supplementary_file like '%CEL.gz'"))
dim(rs_3)
# [1] 29644     2
```

![](https://upload-images.jianshu.io/upload_images/14383117-4b208dc10ece00bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3.2.5 用 `sapply()` 获取所有 table 下的记录数目

```R
getTableCounts <- function(tableName,conn) {
  sql <- sprintf("select count(*) from %s",tableName)
  return(dbGetQuery(conn,sql)[1,1])
}
do.call(rbind,sapply(geo_tables,getTableCounts,con,simplify=FALSE))
#                       [,1]
# gds                   4368
# gds_subset           26094
# geoConvert        14135618
# geodb_column_desc      104
# gpl                  20441
# gse                 114650
# gse_gpl             127070
# gse_gsm            3582196
# gsm                3251485
# metaInfo                 2
# sMatrix              49161
```

### 3.3 格式转换

函数 `goeConvert()` 可以完成 GEO 数据库中各种格式的转换。

查看 "GPL96" 能够转换的格式和每种格式对应的数量：

```R
conversion <- geoConvert('GPL96')
lapply(conversion, dim)
# $gse
# [1] 1092    2
# 
# $gsm
# [1] 41470     2
# 
# $gds
# [1] 365   2
# 
# $sMatrix
# [1] 1015    2
```

分别查看：

```R
conversion$gse[1:5,]
#   from_acc   to_acc
# 1    GPL96  GSE1000
# 2    GPL96 GSE10024
# 3    GPL96 GSE10043
# 4    GPL96 GSE10072
# 5    GPL96 GSE10089
conversion$gsm[1:5,]
#   from_acc     to_acc
# 1    GPL96 GSM1003058
# 2    GPL96 GSM1003059
# 3    GPL96 GSM1003060
# 4    GPL96 GSM1003061
# 5    GPL96 GSM1003062
conversion$gds[1:5,]
#   from_acc  to_acc
# 1    GPL96 GDS1023
# 2    GPL96 GDS1036
# 3    GPL96 GDS1050
# 4    GPL96 GDS1062
# 5    GPL96 GDS1063
conversion$sMatrix[1:5,]
#   from_acc                        to_acc
# 1    GPL96  GSE1000_series_matrix.txt.gz
# 2    GPL96 GSE10024_series_matrix.txt.gz
# 3    GPL96 GSE10043_series_matrix.txt.gz
# 4    GPL96 GSE10072_series_matrix.txt.gz
# 5    GPL96 GSE10089_series_matrix.txt.gz
```

### 3.4 获取 Bioconductor microarray 注释包的 GPL 信息

```R
getBiocPlatformMap(con, bioc=c('hgu133a','hgu95av2'))
#
#                                                     title     gpl bioc_package
# 1          [HG-U133A] Affymetrix Human Genome U133A Array   GPL96      hgu133a
# 2            [HG_U95A] Affymetrix Human Genome U95A Array   GPL91     hgu95av2
# 3 [HG_U95Av2] Affymetrix Human Genome U95 Version 2 Array GPL8300     hgu95av2
#   manufacturer     organism data_row_count
# 1   Affymetrix Homo sapiens          22283
# 2   Affymetrix Homo sapiens          12626
# 3   Affymetrix Homo sapiens          12625
```

### 3.5 GSE 标题词云图

需要用到 `tm` 和 `wordcloud` 这两个包。

```R
library(tm)
library(wordcloud)
gseTitles <- dbGetQuery(con,"select title from gse")
corp <- VCorpus(VectorSource(gseTitles))
corp <- tm_map(corp, removePunctuation)
corp <- tm_map(corp, content_transformer(tolower))
corp <- tm_map(corp, removeNumbers)
corp <- tm_map(corp, function(x)removeWords(x,stopwords()))
term.matrix <- as.matrix(TermDocumentMatrix(corp))
v <- sort(rowSums(term.matrix),decreasing=TRUE)
d <- data.frame(word = names(v),freq=v)
n = 100
wordcloud(d[1:n,]$word,d[1:n,]$freq)
```

![](https://upload-images.jianshu.io/upload_images/14383117-c9ae391b17c6a5c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 3.6 `dplyr` 和 `GEOmetadb.sqlite` 数据库的结合使用

不使用 SQL 语句，而用 `dplyr` 包内的函数也能获得感兴趣的信息。

```R
library(dplyr)
db <- src_sqlite('GEOmetadb.sqlite')
gse_dp <- tbl(db, 'gse')
filter(gse_dp, gse=='GSE2553')
# # Source:   lazy query [?? x 18]
# # Database: sqlite 3.22.0
# #   [YOUR PATH\GEOmetadb\GEOmetadb.sqlite]
#      ID title gse   status submission_date last_update_date pubmed_id summary
#   <dbl> <chr> <chr> <chr>  <chr>           <chr>                <int> <chr>  
# 1  2151 NHGR~ GSE2~ Publi~ 2005-04-21      2013-01-17        16230383 "Sarco~
# # ... with 10 more variables: type <chr>, contributor <chr>, web_link <chr>,
# #   overall_design <chr>, repeats <chr>, repeats_sample_list <chr>,
# #   variable <chr>, variable_description <chr>, contact <chr>,
# #   supplementary_file <chr>
```

## 4. 断开数据库/删除数据库文件

```R
dbDisconnect(con)
```

```R
file.remove('GEOmetadb.sqlite')
```
## References

- **Using the GEOmetadb Package** <https://bioconductor.org/packages/release/bioc/vignettes/GEOmetadb/inst/doc/GEOmetadb.html>
- **Samples per series/dataset in the NCBI GEO database**, NSAUNDERS <https://nsaunders.wordpress.com/2010/01/08/samples-per-seriesdataset-in-the-ncbi-geo-database/>
- **Schema In SQL Server** <https://www.c-sharpcorner.com/UploadFile/ff2f08/schema-in-sql-server/>
- **The SQL LIKE Operator** https://www.w3schools.com/sql/sql_like.asp

#### 今日份的悄咪咪

![](https://upload-images.jianshu.io/upload_images/14383117-277a08be09a5bfd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到拼写错误还是感到十分迷惑(。_。)

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)


