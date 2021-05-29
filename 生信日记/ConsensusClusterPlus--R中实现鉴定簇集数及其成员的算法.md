> ![](https://upload-images.jianshu.io/upload_images/14383117-3e04a75e7408bd63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 无监督分析下鉴定簇集数及成员

> Wilkerson, D. M, Hayes, Neil D (2010). “ConsensusClusterPlus: a class discovery tool with confidence assessments and item tracking.” *Bioinformatics*, **26**(12), 1572-1573. <http://bioinformatics.oxfordjournals.org/content/26/12/1572.abstract>.

## 1. 关于 `ConsensusClusterPlus`

- *Consensus Clustering* 是一种可用于鉴定数据集（比如 microarray 基因表达）中的簇集 (clusters) 成员及其数量的算法。`ConsensusClusterPlus` 则将 *Consensus Clustering* 在 R 中实现了。 

- Jimmy大神说这是他见过最简单的包┑(￣Д ￣)┍

```R
library(ConsensusClusterPlus)
ls("package:ConsensusClusterPlus")
# [1] "calcICL"              "ConsensusClusterPlus"
```

> `ConsensusClusterPlus` function for determing cluster number and class membership by stability evidence. 
>
> `calcICL` function for calculating cluster-consensus and item-consensus.

## 2. ~~好像真的很简单~~ 只是操作简单

使用 `ConsensusClusterPlus` 的主要三个步骤：

- 准备输入数据
- 跑程序
- 计算聚类一致性 (cluster-consensus) 和样品一致性 (item-consensus) 

## 3. 准备输入数据

首先收集用于聚类分析的数据，比如 mRNA 表达微阵列或免疫组织化学染色强度的实验结果数据。输入数据的格式应为矩阵。下面以 ALL 基因表达数据为例进行操作。

```R
library(ALL)
data(ALL)
dataset <- exprs(ALL)
dataset[1:5,1:5]
#              01005    01010    03002    04006    04007
# 1000_at   7.597323 7.479445 7.567593 7.384684 7.905312
# 1001_at   5.046194 4.932537 4.799294 4.922627 4.844565
# 1002_f_at 3.900466 4.208155 3.886169 4.206798 3.416923
# 1003_s_at 5.903856 6.169024 5.860459 6.116890 5.687997
# 1004_at   5.925260 5.912780 5.893209 6.170245 5.615210
```

取矩阵中 MAD 值 top 5000 的数据：

```R
mads <- apply(dataset, 1, mad)
dataset <- dataset[rev(order(mads))[1:5000],]
dim(dataset)
# [1] 5000  128
```

## 4. 运行 `ConsensusClusterPlus `

先设定几个参数：

- **pItem** (item resampling, proportion of items to sample) : 80%
- **pFeature** (gene resampling, proportion of features to sample) : 80%
- **maxK** (a maximum evalulated *k*, maximum cluster number to evaluate) : 6
- **reps** (resamplings,  number of subsamples) : 50
- **clusterAlg** (agglomerative heirarchical clustering algorithm) : 'hc' (hclust)
- **distance** :  'pearson' (1 - Pearson correlation)

```R
# title <- tempdir() ## 虽说是“当前文件夹”，但似乎结果会输出到包的安装路径...
## 所以还是👇
title <- “YOUR PATH”
results <- ConsensusClusterPlus(dataset, maxK = 6,
                                reps = 50, pItem = 0.8,
                                pFeature = 0.8,  
                                clusterAlg = "hc", 
                                distance = "pearson",
                                title = title,
                                plot = "png")  
## 作者这里是pFeature = 1，和前文不符，于是我依然是按0.8输入计算的
```

这时工作路径的文件夹会出现9张图。

查看一下结果：

```R
results[[2]][["consensusMatrix"]][1:5,1:5] 
#         [,1]      [,2]      [,3]    [,4]      [,5]
# [1,] 1.00000 0.9375000 1.0000000 0.90625 1.0000000
# [2,] 0.93750 1.0000000 0.9677419 1.00000 0.9393939
# [3,] 1.00000 0.9677419 1.0000000 0.93750 1.0000000
# [4,] 0.90625 1.0000000 0.9375000 1.00000 0.9062500
# [5,] 1.00000 0.9393939 1.0000000 0.90625 1.0000000
results[[2]][["consensusTree"]] 
# Call:
# hclust(d = as.dist(1 - fm), method = finalLinkage)
# 
# Cluster method   : average 
# Number of objects: 128 
results[[2]][["consensusClass"]][1:5] 
# 01005 01010 03002 04006 04007 
#     1     1     1     1     1 
```

## 4.1 一致性矩阵

分别为图例、*k* = 2, 3, 4, 5 时的矩阵热图。
![](https://upload-images.jianshu.io/upload_images/14383117-304632b558279842.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 4.2 一致性累积分布函数图
![](https://upload-images.jianshu.io/upload_images/14383117-6ea943e92f5a1c52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> This figure allows a user to determine at what number of clusters, *k*, the CDF 
>
> reaches an approximate maximum, thus consensus and cluster con dence is at 
>
> a maximum at this *k*. 

### 4.3 Delta Area Plot 
![](https://upload-images.jianshu.io/upload_images/14383117-a7c1f5ecda72b0cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> The delta area score (y-axis) indicates the relative increase in cluster stability.

### 4.4 Tracking Plot 
![](https://upload-images.jianshu.io/upload_images/14383117-bb3543e650637822.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> This plot provides a view of item cluster membership across different *k* and enables a user to track the history of clusters relative to earlier clusters.

## 5. 计算聚类一致性 (cluster-consensus) 和样品一致性 (item-consensus) 

```R
icl <- calcICL(results, title = title,
               plot = "png")
## 返回了具有两个元素的list，然后分别查看一下
dim(icl[["clusterConsensus"]])
# [1] 20  3
icl[["clusterConsensus"]] 
#       k cluster clusterConsensus
#  [1,] 2       1        0.9402982
#  [2,] 2       2        0.9062500
#  [3,] 3       1        0.8504193
#  [4,] 3       2        0.9062500
#  [5,] 3       3        0.9869781
#  [6,] 4       1        0.9652282
#  [7,] 4       2        0.9045058
#  [8,] 4       3        0.9062500
#  [9,] 4       4        0.9728043
# [10,] 5       1        0.9216686
# [11,] 5       2        0.9145987
# [12,] 5       3        0.9062500
# [13,] 5       4        0.9874950
# [14,] 5       5              NaN
# [15,] 6       1        0.9307379
# [16,] 6       2        0.8897721
# [17,] 6       3        0.7474747
# [18,] 6       4        0.8750000
# [19,] 6       5        0.9885269
# [20,] 6       6        0.6333333
dim(icl[["itemConsensus"]])
# [1] 2560    4
icl[["itemConsensus"]][1:5,] 
#   k cluster  item itemConsensus
# 1 2       1 28032     0.9523526
# 2 2       1 28024     0.9366226
# 3 2       1 03002     0.9686272
# 4 2       1 01005     0.9573623
# 5 2       1 04007     0.9549235
```

### 5.1 Cluster-Consensus Plot 

![](https://upload-images.jianshu.io/upload_images/14383117-3be10b9b21c810ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.2 tem-Consensus Plot 
![](https://upload-images.jianshu.io/upload_images/14383117-b7c3eaea55288dc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





## References

- **ConsensusClusterPlus Tutorial** https://bioconductor.org/packages/release/bioc/vignettes/ConsensusClusterPlus/inst/doc/ConsensusClusterPlus.pdf
- Nowicka, Malgorzata, et al. "**CyTOF workflow: differential discovery in high-throughput high-dimensional cytometry datasets**." *F1000Research* 6 (2017).

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
