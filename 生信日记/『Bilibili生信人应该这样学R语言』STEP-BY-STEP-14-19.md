> 配合[【生信技能树】生信人应该这样学R语言]([https://www.bilibili.com/video/av25643438?p=19](https://www.bilibili.com/video/av25643438?p=19)
) 食用，绝佳。
基本精确到了每分钟，前13P整理好后发。
To be continued...
![](https://upload-images.jianshu.io/upload_images/14383117-203fb87a1adffdac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## P14 学习资源介绍

介绍 R 和 Rstudio，和 P1 内容基本一致。

## P15 与 excel 的区别

> [数据](https://www.ncbi.nlm.nih.gov/Traces/study/?acc=SRP115453)

- **01:51** 加载数据

  ```R
  a=read.table('SraRunTable.txt',header = T,sep = '\t')
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-9173d68fd6ee67e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **03:37** 对第二列求和

  ```R
  > sum(a[,2])
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-257638f1fe43a576.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **04:54** 取第一行

  ```R
  > a[1,]
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-890b2d556bc2bf16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **05:30** R 中自带”固定表头“功能

- **05:35** 取 a 的 前四行+前四列 为 b

  ```R
  > b=a[1:4,1:4]
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-e5149e61e1b3233e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **09:50** 取 b 的第一列为 d

  ```R
  d=b[,1]
  ```

  这时用 `class()` 函数查看 d, 返回 `[1] "factor"`，需要在操作的开始阶段设置：

  ```R
  options(stringsAsFactors = F)
  ```

  重新读入文件，得到：

  ![](http://upload-images.jianshu.io/upload_images/14383117-76d538cfc1d12a9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  R tips: 代码加上`()`，赋值同时 print 

- **10:44** 创建矩阵

  ```R
  > e=matrix(1:10,2)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-44aa054df497b145.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **12:40** 在 R 中实现筛选

  excel中：

  ![](http://upload-images.jianshu.io/upload_images/14383117-b711995175e91fbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > a[,1]=='WXS'
  > table(a[,1]=='WXS')
  > which(a[,1]=='WXS')
  > f=a[which(a[,1]=='WXS'),]
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-d81a7c386f8a08aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](http://upload-images.jianshu.io/upload_images/14383117-4bba0b439fd6f915.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **18:35** 对某一列不同元素计数

  ```R
  > table(a[,18])
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-6ab0ce70df028e7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](http://upload-images.jianshu.io/upload_images/14383117-3b08bfb007f2916b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](http://upload-images.jianshu.io/upload_images/14383117-bf3145d5d446a554.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P16 简单统计及数学函数

- **01:44** 将 SraRunTable.txt 另存为 .csv 格式，在 R 中进行后续操作

  ```R
  > a=read.csv('SraRunTable2.csv')
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-805cd3e1c0df6614.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **02:58** 求某列的平均值

  ```R
  > mean(a$MBases)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-5ff5c4eb3538a8bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  分别筛选出 Assay_Type 为 ‘WXS’/‘RNA-Seq’ 的 ‘MBases’ 的均值
  
  ```R
  > wxs=a[a[,1]=='WXS',]
  > rna=a[a[,1]!='WXS',]
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-41fb816a8cd5d14f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-49ed8a7cc7c54323.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
  ```R
  > mean(wxs$MBases)
  > mean(rna$MBases)
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-4973fa754cf5bd15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **05:25** t 检验，根据 p 值判断显著性

  ```R
  > t.test(rna$MBases,wxs$MBases)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-d48614cdee8b1604.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **06:28** 求某列的最大值

  ```R
  > max(a$MBases)
  > max(wxs$MBases)
  > max(rna$MBases)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-13661caa55e43bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **06:51** 求某列的最小值、下四分位数、中位数、上四分位数、最大值

  ```R
  > fivenum(a$MBases)
  > fivenum(wxs$MBases)
  > fivenum(rna$MBases)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-092c183d928f8c9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **11:20** 求某列的绝对中位差 (Median Absolute Deviation)

  ```R
  > mad(a$MBases)
  ```



## P17 基础语法

- **00:20** 正则表达式

  在 shell 中，用 `grep` 命令查看
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-1042c832d2f377a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
  在 R 中
  
  ```R
  > grep('WXS',a[,1])
  > grepl('WXS',a[,1])
  ```
  
 
  
  ```R
  > wxs=a[a[,1]=='WXS',]
  > wxs1=a[grep('WXS',a[,1]),]
  > wxs2=a[grepl('WXS',a[,1]),]
  > wxs3=a[which(a[,1]=='WXS'),]
  ```
  
  以上4条命令得到的结果相同
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-b75861d0da6250d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **03:51** 取某一列数据中特定的部分

  ```R
  > tmp=a$Library_Name
  > strsplit(tmp,',')
  > strsplit(tmp,',')[[1]][2]
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-e13691e03d0dcc4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  需要引入循环

  ```R
  > lapply(strsplit(tmp,','), function(x){cat(x)})
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-a30f4051b785acfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > l=strsplit(tmp,',')
  > for (x in l) {
  +   print(x[2])
  + }
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-841d5f25bf81c2cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  `for` 循环可以简化为 `lapply`循环

  ```R
  > lapply(l, function(x){x[2]})
  ```

- **11:21** 对提取出的数据进行 `unlist` 操作

  ```R
  > tmp=unlist(lapply(l, function(x){x[2]}))
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-c7c1dfef25322ca1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **12:55** 替换 ‘PE’

  ```R
  > sub('PE','',tmp)
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-a6e24c3f7e8af43e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **13:54** 去重

  ```R
  > unique(sub('PE','',tmp))
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-398ad61f7af877ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
  
  

## P18 高级数据处理技巧

- **01:09** 获取表达矩阵

  ```R
  > library(devtools)
  > library(airway)
  > library(CLL)
  > suppressPackageStartupMessages(library(CLL))
  > data(sCLLex)
  > exprSet=exprs(sCLLex)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-95d64c635910fc1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **05:28** 用 `apply ` 求基因的表达量平均值

  ```R
  > apply(exprSet, 2, mean)
  ```

  >  for a matrix 1 indicates rows, 2 indicates columns.

  ![](http://upload-images.jianshu.io/upload_images/14383117-573292e5c615475a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **06:27** 基因表达量均值的 `boxplot`

  ```R
  > boxplot(exprSet)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-de15102a64427263.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **09:03** Tips for R

  `vignette()` 函数可以调出 R 包的介绍

- **09:21** `str_split` 和 `strsplit` 的区别

  ```R
  > str_split(a$Library_Name,',')
  > str_split(a$Library_Name,',',simplify = T)
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-ae31ed1b50d0833e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](http://upload-images.jianshu.io/upload_images/14383117-0a8d19d54a3efd10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

  > `simplify = T` 直接将数据提取为 data.frame

  取第二列：

  ```R
  > str_split(a$Library_Name,',',simplify = T)[,2]
  ```

  ![](http://upload-images.jianshu.io/upload_images/14383117-7ffad0cc16e90dcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **10:38** 用 `do.call` `rbind` 解决同样的问题

  ```R
  > do.call(rbind,str_split(a$Library_Name,','))
  > do.call(rbind,str_split(a$Library_Name,','))[,2]
  ```

  

## P19 绘图该如何学
- **00:38** 重新读入 a

  ```R
  > a=read.csv('SraRunTable2.csv',stringsAsFactors = F)
  ```

- **01:21** `boxplot` 

  ```R
  > boxplot(a$MBases~a$Assay_Type)
  > t.test(a$MBases~a$Assay_Type)$p.value
  > p=t.test(a$MBases~a$Assay_Type)$p.value
  > paste('P value=',p)
  > title(main=paste('P value=',p))
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-3e9d6f23e04c9589.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **04:28** 换 R 包，加参数，~~拯救丑图~~ 美化图片

  ```R
  > ggboxplot(a,"Assay_Type","MBases",color = "Assay_Type",
  +           palette = c("#00AFBB","#E7B800"),add = "jitter") + stat_compare_means(method = "t.test")
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-c696ab1805346ed9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **11:49** `ggplot2`

  ```R
  >ggplot(a,aes(Assay_Type,MBases))+geom_boxplot(aes(col=Assay_Type))+theme_bw()+geom_jitter(aes(col=Assay_Type))
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-0bea5c837b0fd426.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **19:35** `hclust` 画 cluster dendrogram （树状图）

  ```R
  > library(CLL)
  > data("sCLLex")
  > e=exprs(sCLLex)
  > apply(e,2,mean)
  > plot(hclust(dist(t(e))))
  ```
  
  ![](http://upload-images.jianshu.io/upload_images/14383117-fa07ccdea36cf2cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

