> ![](https://upload-images.jianshu.io/upload_images/14383117-bff86e44397b8f60.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
昨日迷惑在这里 [**一次完全失败的尝试**](https://www.jianshu.com/p/6c7af178f79d)
后来发现，应该是数据库更新问题...?




```r
> lookUp(igenes, "hgu95av2", "UNIGENE")
$`31485_at`
[1] "Hs.169284"

$`31486_s_at`
[1] "Hs.268515"

$`31487_at`
[1] "Hs.258563"

$`31488_s_at`
[1] "Hs.78771"

$`31489_at`
[1] "Hs.532632"

$`31490_at`
[1] "Hs.517898"

$`31491_s_at`
[1] "Hs.599762"

$`31492_at`
[1] "Hs.314359"

$`31493_s_at`
[1] NA

$`31494_at`
[1] NA

$`31495_at`
[1] "Hs.458346"

$`31496_g_at`
[1] NA

$`31497_at`
[1] NA

$`31498_f_at`
[1] NA

$`31499_s_at`
[1] "Hs.372679" "Hs.694258" "Hs.736230"

```
这时候再来一波 `unlist` 和 `as.character` 的操作的话，最后会有17个啊！！！
```r
 [1] "Hs.169284" "Hs.268515" "Hs.258563" "Hs.78771"  "Hs.532632"
 [6] "Hs.517898" "Hs.599762" "Hs.314359" NA          NA         
[11] "Hs.458346" NA          NA          NA          "Hs.372679"
[16] "Hs.694258" "Hs.736230"
```
于是，把 UniGene ID 和 UniProt ID 一样，以 list 格式放着。

----
所以接下来是**第一种方法**，是用 `annotate` 这个包里的函数 `htmlpage()` 解决了一切。
```r
library(annotate)
data("sample.ExpressionSet")
igenes <- featureNames(sample.ExpressionSet)[246:260]
library(hgu95av2.db)

gb_t <- as.character(unlist(lookUp(igenes, "hgu95av2", "ACCNUM")))
ll_t <- as.character(unlist(lookUp(igenes, "hgu95av2", "ENTREZID")))
ug_t <- lookUp(igenes, "hgu95av2", "UNIGENE")
names(ug_t) <- NULL
sp_t <- lookUp(igenes, "hgu95av2", "UNIPROT")
names(sp_t) <- NULL

for(i in 1:15) {
  if(i == 9 || i == 10 || i ==12 ||
     i ==13 || i == 14)
    ll_t[i] <- "---"
}

for(i in 1:15) {
  if(i == 9 || i == 10 || i ==12 ||
     i ==13 || i == 14)
    sp_t[[i]][1] <- "&nbsp;"
}
for(i in 1:15) {
  if(i == 9 || i == 10 || i ==12 ||
     i ==13 || i == 14)
    ug_t[[i]][1] <- "&nbsp;"
}


name_t <- as.character(unlist(lookUp(igenes, "hgu95av2", "GENENAME")))
name_t
for(i in 1:length(names)) {
  if(i == 9 || i == 10 || i ==12 ||
     i ==13 || i == 14)
    name_t[i] <- "&nbsp;"
}

dat <- exprs(sample.ExpressionSet)[igenes,1:10]
FC <- rowMeans(dat[igenes,1:5]) - rowMeans(dat[igenes,6:10]) 
pval <- esApply(sample.ExpressionSet[igenes,1:10], 1, function(x) t.test(x[1:5], x[6:10])$p.value)
tstat <- esApply(sample.ExpressionSet[igenes,1:10], 1, function(x) t.test(x[1:5], x[6:10])$statistic)

genelist_t <- list(igenes, ug_t, ll_t, gb_t, sp_t)
filename_t <- "Interesting_genes_t.html" 
title_t <- "An Artificial Set of Interesting Genes" 
othernames_t <- list(name_t, round(tstat, 2), round(pval, 3), round(FC, 1), round(dat, 2))
head_t <- c("Probe ID", "UniGene", "LocusLink", "GenBank", "SwissProt", "Gene Name", "t-statistic", "p-value",
          "Fold Change", "Sample 1", "Sample 2", "Sample 3", "Sample 4", "Sample 5", "Sample 6",
          "Sample 7", "Sample 8", "Sample 9", "Sample 10")
repository_t <- list("affy", "ug", "en", "gb", "sp") 
htmlpage(genelist_t, filename_t, title_t, othernames_t, head_t, repository = repository_t) 
```
![](https://upload-images.jianshu.io/upload_images/14383117-efcd5e345f752bd5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/14383117-0ff970882d3bfdd9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


完美。

---
**第二种方法** 是 Jimmy 大神写的函数，导出的 HTML 比上面的好看多了，我做了些~~卑微~~小小的修改，嵌套了一下，当然最后的结果...还需要再调整。
```r
gene2html_altered <- function(GeneList,file){
  # GeneList = igenes;
  # file = 'interested_genes_2.0.html'
  library(annotate)
  library(hgu95av2.db)
  pb2GenB <- as.character(unlist(lookUp(igenes, "hgu95av2", "ACCNUM")))
  pb2ENTZ <- as.character(unlist(lookUp(igenes, "hgu95av2", "ENTREZID")))
  pb2UNIG <- lookUp(igenes, "hgu95av2", "UNIGENE")
  names(pb2UNIG) <- NULL
  pb2UNIP <- lookUp(igenes, "hgu95av2", "UNIPROT")
  names(pb2UNIP) <- NULL
  pb2GenNm <- as.character(unlist(lookUp(igenes, "hgu95av2", "GENENAME")))
  for(i in 1:15) {
    if(i == 9 || i == 10 || i ==12 ||
       i ==13 || i == 14)
      pb2ENTZ[i] <- "---"
  }
  for(i in 1:15) {
    if(i == 9 || i == 10 || i ==12 ||
       i ==13 || i == 14)
      pb2UNIG[i] <- "---"
  }
  for(i in 1:15) {
    if(i == 9 || i == 10 || i ==12 ||
       i ==13 || i == 14)
      pb2UNIP[[i]][1] <- "&nbsp;"
  }
  for(i in 1:15) {
    if(i == 9 || i == 10 || i ==12 ||
       i ==13 || i == 14)
      pb2UNIG[[i]][1] <- "&nbsp;"
  }
  for(i in 1:length(names)) {
    if(i == 9 || i == 10 || i ==12 ||
       i ==13 || i == 14)
      pb2GenNm[i] <- "&nbsp;"
  }
  
 
  FC <- rowMeans(dat[igenes,1:5]) - rowMeans(dat[igenes,6:10]) 
  pval <- esApply(sample.ExpressionSet[igenes,1:10], 1, function(x) t.test(x[1:5], x[6:10])$p.value)
  tstat <- esApply(sample.ExpressionSet[igenes,1:10], 1, function(x) t.test(x[1:5], x[6:10])$statistic)  
  
  createLink <- function(base,val) {
    sprintf('<a href="%s" class="btn btn-link" target="_blank" >%s</a>',base,val) ##target="_blank" 
  }
  gene_info <- data.frame(ProbeIDs = igenes,
                          UniGene = createLink(paste0("https://www.ncbi.nlm.nih.gov/UniGene/clust.cgi?ORG=Hs&CID=", pb2UNIG), pb2UNIG),
                          LocusLink = createLink(paste0("https://www.ncbi.nlm.nih.gov/entrez/query.fcgi?db=gene&cmd=Retrieve&dopt=Graphics&list_uids=", pb2ENTZ), pb2ENTZ),
                          GenBank = createLink(paste0("https://www.ncbi.nlm.nih.gov/entrez/query.fcgi?db=Nucleotide&cmd=search&term=", pb2GenB), pb2GenB),
                          SwissProt = createLink(paste0("http://www.uniprot.org/uniprot/", pb2UNIP), pb2UNIP),
                          GeneNames = pb2GenNm,
                          tstatistic = round(tstat, 2),
                          pvalue = round(pval, 3),
                          FoldChange = round(FC, 1),
                          stringsAsFactors = F) 
  
  y <- DT::datatable(gene_info,escape = F,rownames=F)
  DT::saveWidget(y,file)
  
}

gene2html_altered(igenes,'interested_genes_2.0.html')
```
![](https://upload-images.jianshu.io/upload_images/14383117-a8e8ca4e414e7b8c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/14383117-ea95fcfbb081bbdd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
