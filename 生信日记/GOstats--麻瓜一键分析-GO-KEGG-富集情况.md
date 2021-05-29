> 本菜甚至是不好意思PO这篇
![](https://upload-images.jianshu.io/upload_images/14383117-20cfd2cf91791293.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Tools for manipulating GO and microarrays

> Falcon S, Gentleman R (2007). “Using GOstats to test gene lists for GO term association.” *Bioinformatics*, **23**(2), 257-8.

## 1. 标准开头

关于 `GOstats` ，又是真の大佬写的包。

包里有一系列能分析 GO 和微阵列数据的工具, 可以通过超几何分布检验的方法分析 GO/KEGG 的富集情况。

## 2. GO 富集分析

### 2.1 设置参数

构建 `GOHyperGParams` 实例前，需要设置以下参数：

> **geneIds:** 所选择的基因 ID.
>
> **universeGeneIds:** 全部基因 ID.
>
> **annotation:** 所对应的注释包名称。
>
> **ontology:** GO 分类，即 BP, CC, MF. 每次检测只能输入一个。
>
> **pvalueCutoff:** p 值。
>
> **conditional:**  TRUE-条件超几何检验，FALSE-标准超几何检验
>
> **testDirection:** “over” or “under”

```R
params <- new("GOHyperGParams",
              geneIds = genes, 
              universeGeneIds = universe, 
              annotation = "org.Hs.eg.db",
              ontology = "MF", 
              pvalueCutoff = 0.05, 
              conditional = FALSE, 
              testDirection = "over")
```
### 2.2 `hyperGTest()`

输入前面构建的 `params` ，一步得到分析结果：


```R
hgOver <- hyperGTest(params)
hgOver
# Gene to GO MF  test for over-representation 
# 996 GO MF ids tested (502 have p < 0.05)
# Selected gene set size: 411 
#     Gene universe size: 16969 
#     Annotation package: org.Hs.eg 
head(summary(hgOver))
#       GOMFID       Pvalue OddsRatio  ExpCount Count Size
# 1 GO:0019829 2.761450e-32  89.30268 0.8961636    25   37
# 2 GO:0022853 7.895069e-32  82.42826 0.9203842    25   38
# 3 GO:0042625 7.895069e-32  82.42826 0.9203842    25   38
# 4 GO:0042626 1.021258e-30  38.01563 1.5016795    29   62
# 5 GO:0043492 1.558530e-30  33.35150 1.6712240    30   69
# 6 GO:0015399 3.001251e-29  32.15539 1.6470034    29   68
#                                                               Term
# 1                              cation-transporting ATPase activity
# 2                    active ion transmembrane transporter activity
# 3            ATPase coupled ion transmembrane transporter activity
# 4 ATPase activity, coupled to transmembrane movement of substances
# 5               ATPase activity, coupled to movement of substances
# 6                primary active transmembrane transporter activity
```

## 3. KEGG 富集分析

整个流程都和 GO 分析如出一辙，稍微变一下对象和参数即可。

### 3.1 设置参数

需设置参数： **geneIds**, **universeGeneIds**, **annotation**, **pvalueCutoff**, **testDirection**.

```R
params2 <- new("KEGGHyperGParams",
               geneIds = genes, 
               universeGeneIds = universe, 
               annotation = "org.Hs.eg.db",
               pvalueCutoff = 0.05, 
               testDirection = "over")
```

### 3.2 `hyperGTest()`

```R
kOver <- hyperGTest(params2)
kOver
# Gene to KEGG  test for over-representation 
# 175 KEGG ids tested (65 have p < 0.05)
# Selected gene set size: 282 
#     Gene universe size: 5077 
#     Annotation package: org.Hs.eg
head(summary(kOver))
#   KEGGID       Pvalue OddsRatio  ExpCount Count Size
# 1  04970 2.204029e-18 11.154157  4.276367    30   89
# 2  01100 2.703714e-17  3.052904 54.295451   114 1130
# 3  00071 4.028679e-17 20.352665  2.066110    21   43
# 4  00280 3.631982e-13 14.583042  2.114159    18   44
# 5  04972 6.256463e-13  7.464167  4.852956    26  101
# 6  04976 6.176347e-12  8.910115  3.411484    21   71
#                                         Term
# 1                         Salivary secretion
# 2                         Metabolic pathways
# 3                     Fatty acid degradation
# 4 Valine, leucine and isoleucine degradation
# 5                       Pancreatic secretion
# 6                             Bile secretion
```

所以 `hyperGTest()` 就是一个让人不明原理但又有很酷炫输出的~~傻瓜~~黑箱函数。

---

# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
