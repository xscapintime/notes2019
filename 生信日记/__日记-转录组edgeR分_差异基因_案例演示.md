> 菜🐣的第一次正式上手R，相当于翻译了 `edgeR` 这个包的 UserGuide，跑了第一个 case
> 眼看写了这么多字，还是发一下比较好，万一有人看呢
![](https://upload-images.jianshu.io/upload_images/14383117-6023d7a38e434ae9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### edgeR简介

edgeR 可以应用于任何可产生基因组特征数据（read counts）的技术，能够为 RNA-seq 实验中评估差异表达、ChIP-seq 实验中差异标记提供统计程序。该R包具备适用于多组实验的精准统计法，同时还具备广义线性模型（glms）的统计学方法——适用于不同复杂程度的多因素实验。有时人们将前者称为 classic edgeR，将后者称为 glm edgeR。然而上述两种方法是互补的，并且时常在数据分析中被结合使用。大多数 glm 函数可以通过函数名称中的 "glm" 识别，这类函数可利用似然比检验或拟似然F检验检测差异表达。edgeR 的功能的一个重要特点是，不论 classic 和 glm 两种方法，都属于经验贝叶斯方法，从而能够在实验只具有最小水平的生物学重复时，依然能够判断出基因特异的生物学差异。edgeR 可应用于不同水平的差异表达，如基因、外显子、转录本、标签等，分析外显子水平时可轻易地检测出可变剪切或异构体特异性差异表达。

### 案例分析

#### RNA-seq —— 口腔肿瘤 vs 对应的正常组织 (RNA-Seq of oral carcinomas vs matched normal)

分析的目的是检测肿瘤和正常组织对比下差异表达的基因，这个案例可以体现 edgeR 中 GLM 法的工作能力。

##### 1.edgeR 安装

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("edgeR")
```

##### 2.数据读取

下载文章 [Tumor Transcriptome Sequencing Reveals Allelic Expression Imbalances Associated with Copy Number Alterations](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0009317/) 中的 Table S1。

- 去除多余列项、重命名列名

  ```R
  > rawdata <- read.delim("TableS1.txt", check.names=FALSE, stringsAsFactors=FALSE)
  ```

  ```R
  > rawdata <- rawdata[,c(1:9)]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-8a1246fce44b73d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/647/format/webp)

  ```R
  > names(rawdata)[1:9]<-c("RefSeqID","Symbol","NbrOfExons","8N","8T","33N","33T","51N","51T")
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-cf336c4aa5e63ecd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/731/format/webp)

  > 这里不小心把表格的一二行的行数弄没了，后面需要注意数字
  >
  > > 结果到`DEGList`这一步时出现报错“The count matrix is a data.frame instead of a matrix and the first 6 columns are non-numeric.” 用了as.matrix()后表格里所有内容又都变成了factor...😓
  >
  > **所以，最后还是直接在excel表格里解决了行名💨
> 更新：其实这个很好解决，然这是第一次正式，没错“正式”，用R....



- 加载edgeR, 用`DEGList`构建列表

  ```R
  > library(edgeR) Loading 
  > y <- DGEList(counts=rawdata[,4:9], genes=rawdata[,1:3]) 
  ```

##### 3.注释

- 筛选转录本

  这篇文章发表已经是几年前了，数据中一些 RefSeq ID 可能和现在一般使用的 RefSeq ID 有出入，所以只需要保留`org.HS.eg.db`中有的、具有 NCBI 注释的那部分转录本。

  ```R
  > library(org.Hs.eg.db) 
  > idfound <- y$genes$RefSeqID %in% mappedRkeys(org.Hs.egREFSEQ) 
  > y <- y[idfound,] 
  > dim(y) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-dfe0c811f47c36b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/544/format/webp)

  > 可以看出有15548个基因

- 将 Entrez Gene ID 加入注释

  ```R
  > egREFSEQ <- toTable(org.Hs.egREFSEQ) 
  > head(egREFSEQ) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-c258b98d77b30d92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/504/format/webp)

  ```R
  > m <- match(y$genes$RefSeqID, egREFSEQ$accession) 
  > y$genes$EntrezGene <- egREFSEQ$gene_id[m] 
  ```

- 利用 Entrez Gene ID 更新 gene symbol

  ```R
  > egSYMBOL <- toTable(org.Hs.egSYMBOL) 
  > head(egSYMBOL) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-41fbb2bc67412586.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/398/format/webp)

  ```R
  > m <- match(y$genes$EntrezGene, egSYMBOL$gene_id) 
  > y$genes$Symbol <- egSYMBOL$symbol[m] 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-f4fb9cbf12a0f950.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 5.筛选及归一化 （Normalization）

- 筛选出 count 数最多的转录本

  每个 gene symbol 保留一个转录本

  ```R
  > o <- order(rowSums(y$counts), decreasing=TRUE) > y <- y[o,] 
  > d <- duplicated(y$genes$Symbol) 
  > y <- y[!d,] 
  > nrow(y) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-adcee651fd2bac4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/401/format/webp) 

- 重新计算文库大小

  ```R
  > y$samples$lib.size <- colSums(y$counts) 
  ```

- 将 Use Entrez Gene ID 设为行名

  ```R
  > rownames(y$counts) <- rownames(y$genes) <- y$genes$EntrezGene 
  > y$genes$EntrezGene <- NULL 
  ```

- TMM归一化 (trimmed mean of M-values normalization)

  ```R
  > y <- calcNormFactors(y) 
  > y$samples 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-5aa94c60c1a94f1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/266/format/webp)

##### 6.数据挖掘 (Data Exploration)

- 利用`plotMDS`绘制MDS (Multidimensional scaling) 图 

  首先应该分析样本的离群值和其他关系，函数`plotMDS`能够生成样本之间距离与生物差异系数 (biological coefficient of variation, BCV) 相对应的图表。

  ```R
  > plotMDS(y) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-97ba942ac7b65a6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/634/format/webp)图中横坐标将肿瘤样本和正常样本区分开来（左T右N），纵坐标则大致对应了患者编码，这些应证了样本的配对特性。可以看出肿瘤样本的分布比正常样本更不均匀。

##### 7.设计矩阵

- 检测差异表达

  在拟合负二项 GLM 之前，需要根据实验设计优化设计矩阵。接下来需要在同一患者的肿瘤和正常组织样本中检测差异表达，即调整患者之间的差异。统计学意义上，这是把患者作为区组因子的一种可加线性模型。

  ```R
  > Patient <- factor(c(8,8,33,33,51,51)) 
  > Tissue <- factor(c("N","T","N","T","N","T")) 
  > data.frame(Sample=colnames(y),Patient,Tissue)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-c6b838f0ffa0d05c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/415/format/webp)

  ```R
  > design <- model.matrix(~Patient+Tissue) 
  > rownames(design) <- colnames(y)
  > design
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-7da211b3778e535a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/411/format/webp)

  这种可加模型适用于成对设计或具批次效应 (batch effect) 的实验。

##### 8.估计数据分布

- 估计数据组的负二项分布

  ```R
  > y <- estimateDisp(y, design, robust=TRUE) 
  > y$common.dispersion 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-4eb757841b83deb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/376/format/webp)

  公共离散值 (common dispersion) 的平方根，即生物差异  (biological variation) 的差异系数 (coefficient of variation)，为0.4。
  
  ```R
  > plotBCV(y) 
  ```
  
  ![](https://upload-images.jianshu.io/upload_images/14383117-4bbf3128e1264fd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/634/format/webp)

##### 9.差异表达

- 拟合基因层面 GLM

  ```r
  > fit <- glmFit(y, design) 
  ```

- 肿瘤样本 vs 正常组织，做似然比检验，列出差异最为显著的基因

  ```r
  > lrt <- glmLRT(fit) 
  > topTags(lrt) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-1520a21b6cb63f24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/676/format/webp)

  这个检验类似配对t检验。排列靠前的标签/基因的p值、FDR都很小，差异倍数 (FC) 很大。

  进一步地👇

- 单个样本中这些基因的每百万 count 数 (counts-per-million)

  ps.根据p值排序

  ```r
  > o <- order(lrt$table$PValue) 
  > cpm(y)[o[1:10],] 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-0c024aab77cd7ce1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/610/format/webp)

  可以看出，这些基因在肿瘤样本和正常组织中的变化，在三个患者中是一致。

- 以 FDR（错误发现率）为5%，查看差异表达基因的数量

  ```r
  > summary(decideTests(lrt)) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-372436c57893356d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/233/format/webp)

  

- 绘制 log-FC 与 log-cpm 均值 - 差异图 (mean-difference plot)
图中的蓝色横线代表2倍差异 (2-fold changes)。

  ```r
  > plotMD(lrt) 
  > abline(h=c(-1, 1), col="blue") 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-4eb24d1064fe923b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


  

##### 10.GO分析

- 针对生物学过程 (biological process, BP)做GO分析

  ```r
  > go <- goana(lrt) 
  > topGO(go, ont="BP", sort="Up", n=30, truncate=30) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-890fa6b55b66c13e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700/format/webp)

  肿瘤中表达上调的基因可能与细胞分化、细胞迁移、组织形态发生相关。

### Setup Info

![](https://upload-images.jianshu.io/upload_images/14383117-618cd7b24549d414.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/573/format/webp)

