> ![](https://upload-images.jianshu.io/upload_images/14383117-b8db14e181856e38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当只有自己发生状况时，归结给运气和自己太菜；当别人发生同样的状况时，归结给共同的客观原因，比如网速太慢、电脑太烂；当伟大的互联网告诉你有更多的人遭遇了同样的困惑，才终于投向科学，怪罪无知，无知当然是自己的无知。

## 状况1:  GSEA-GO 同样的设定返回了不同的结果

在做 jimmy 大神布置的作业：对比 Gorilla, clusterProfiler, topGO 三种工具

作业做到 GSEA 法 

```R
## clusterProfiler GSEA GO-BP
library(clusterProfiler)
library(hgu133plus2.db)
load("genelist_ENTREZID_Decr_pVal.Rdata")

### with decreasing p.val
gseaGO_BP <- gseGO(geneList     = entrezlist_dcrpv,
                   OrgDb        = hgu133plus2.db,
                   keyType      = "ENTREZID",
                   ont          = "BP",
                   nPerm        = 1000,   ## 排列数
                   minGSSize    = 5,
                   maxGSSize    = 500,
                   pvalueCutoff = 0.95,
                   verbose      = TRUE)  ## 不输出结果
gseaGO_BPresult <- gseaGO_BP@result
save(gseaGO_BPresult, file = "cp_gseaGO_BPresult.Rdata")
```

得到的结果是下面这样的，出现了65个条目和10个条目两种，似乎还有0条，但手慢无截图

![](https://upload-images.jianshu.io/upload_images/14383117-9567e31918bf40f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/899/format/webp)

![](https://upload-images.jianshu.io/upload_images/14383117-22c9461a2422d778.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/892/format/webp)

当时的想法是这样的：

> 可能和网络也有关系？几次输入同样的参数，得到的结果并不一样....有时是报错 no term, 迷惑desu

毕竟日常网络不好，总天真的以为5G来了一切就解决了┑(￣Д ￣)┍

## 状况2:  GSEA-KEGG 同样的设定返回了不同的结果

没有错，前后呼应，这是自称资质平平送老迎新的技能树资深半席讲师遇到的状况。依然是同样的参数，不同的结果。

![](https://upload-images.jianshu.io/upload_images/14383117-e2c4a544cebd9571.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/14383117-07318332ba961e5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

讨论：也许这有一定的随机性？

后来果然发现 `gseKEGG()` 的一个逻辑参数 `seed` 是可以设置的，默认值为 `FALSE`, 那么就惊喜地设置为 `TRUE`, 又惊喜地发现了依然不同的结果。(sad

## 网友们的困惑

回到最初的问题，为什么每次会不一样呢？第二个问题，`seed` 这个参数到底有没有用？

自己思索无果就求助于互联网（然而其实应该别瞎想直接google

大部分第一个问题基本没有已解决的下文，第二个问题倒是终于发现了这位**心明眼亮**的选手：

![](https://upload-images.jianshu.io/upload_images/14383117-04853b6f873d8f38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然也有人说并没有用....

![](https://upload-images.jianshu.io/upload_images/14383117-7fd7a5da24dea413.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不如自己动手尝试：

```R
set.seed(1984)
gseaGO_BP3 <- gseGO(geneList     = entrezlist_FC,
                    OrgDb        = hgu133plus2.db,
                    keyType      = "ENTREZID",
                    ont          = "BP",
                    nPerm        = 1000,   ## 排列数
                    minGSSize    = 5,
                    maxGSSize    = 500,
                    pvalueCutoff = 0.05,
                    verbose      = TRUE,
                    seed         = TRUE)
```

第一次：`no term enriched under specific pvalueCutoff...`

第二次：结果又出现辽

![](https://upload-images.jianshu.io/upload_images/14383117-cfc99e6f97266564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看来seed确实是没用的。

另一位心明眼亮的网友如是说：

![](https://upload-images.jianshu.io/upload_images/14383117-85f867a0cc6a1f40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再回头去看，`gseGO()` 和 `gseKEGG()` 默认的方法是 "fgsea".

> We implemented GSEA algorithm proposed by Subramanian(Subramanian et al. [2005](https://yulab-smu.github.io/clusterProfiler-book/chapter2.html#ref-subramanian_gene_2005)). Alexey Sergushichev implemented an algorithm for fast GSEA analysis in the [fgsea](http://bioconductor.org/packages/fgsea)(S., [n.d.](https://yulab-smu.github.io/clusterProfiler-book/chapter2.html#ref-alex_fgsea)) package.
>
> In [DOSE](http://bioconductor.org/packages/DOSE)(Yu et al. [2015](https://yulab-smu.github.io/clusterProfiler-book/chapter2.html#ref-yu_dose_2015)), user can use GSEA algorithm implemented in `DOSE` or `fgsea` by specifying the parameter `by="DOSE"` or `by="fgsea"`. By default, [DOSE](http://bioconductor.org/packages/DOSE)use `fgsea` since it is much more fast.
>
>  *<https://yulab-smu.github.io/clusterProfiler-book/>* 

而 fgsea 算法的文章里有几段话（反正公式是看不懂的也就看点人话猜一下这亚子

> Gene set enrichment analysis is a very widely used method for analyzing gene expression data. It allows to select from an a priori defined list of gene sets those which have non-random behavior in a considered experiment. 
>
> The method has a major drawback of being relatively slow....That can be done in a straightforward manner by sampling random gene sets.
>
> Instead of generating nm independent random gene set for each permutation and each gene set we will generate only n radom gene sets of size K.
>
> The preranked gene set enrichment analysis takes as input two objects: an array of gene statistic values S and a list of query gene sets P. The goal of the analysis is to determine which of the gene sets from P has a non-random behavior.
>
> *Sergushichev A. An algorithm for fast preranked gene set enrichment analysis using cumulative statistic calculation[J]. BioRxiv, 2016: 060012.*

大概就是说传统的GSEA方法很慢，可以通过对随机的基因集抽样解决这个问题，也就顾名思义是 a fast gene set enrichment analysis (FGSEA) 了。

与此同时四通八达的 jimmy 大神去问了神包 `clusterProfiler` 的作者，神通广大的Y叔——
Y叔回答：“**nPerm次数多点就行 seed没用**”


毕竟👇

> For each p ∈ P we need to find the enrichment statistic value and to calculate a p-value of this not to be random. To calculate a p-value for gene set p we can obtain an empirical null distribution by sampling n random gene sets of the same size as p.
>
> *Sergushichev A. An algorithm for fast preranked gene set enrichment analysis using cumulative statistic calculation[J]. BioRxiv, 2016: 060012.*

所以，多试试总会得到想要的。

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
