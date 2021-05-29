> 终于整理好了
![](https://upload-images.jianshu.io/upload_images/14383117-bf497fc1fdfa42c0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## P1 介绍R语言及Rstudio编辑器
- **00:45** 下载R语言软件 [for win](https://cran.r-project.org/bin/windows/base/)

- **00:53** 下载Rstudio [Rstudio](https://www.rstudio.com/products/rstudio/download/)

- **01:47** Rstudio界面

  ![](https://upload-images.jianshu.io/upload_images/14383117-0d3350b266bab29e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:45** 安装R包

  复制代码到左上Source区块。每点击一行，Console区块出现一行命令。Or, 一次性把所有命令粘贴到Console区块，但无法查看/控制进程。键盘↑↓键可以按顺序调出已经输入的命令。

- **5:20** `plot()`画图

  在未开窗口，e.g. `png(’temp.png‘)`，的情况下，`plot()`绘制出的图像在右下“Plots”窗口显示。

- **06:18** `.libPaths()`查看包的安装路径

- **08:51** `?function`查询函数帮助文档

  e.g. `?subString`

  ![](https://upload-images.jianshu.io/upload_images/14383117-d84f19ebb6bdf5a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- **10:25** 查看当前路径`getwd()`，设置工作路径`setwd()`

  
## P2 R语言基础变量讲解

- **01:55** 创建向量，`c()`, e.g. `a=c(1,2,3)`

- **01:58** 查看向量类型，`class()`

- **02:54** 创建向量

  `a=1:10`

   ![](https://upload-images.jianshu.io/upload_images/14383117-c41ad8c713702460.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  `a=LETTERS` （R语言内置常量Built-in Constants）

  ![](https://upload-images.jianshu.io/upload_images/14383117-b4f88fd59dd4637b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:20** 取内置常量的一部分

  `LETTERS[1:10]`

  ![](https://upload-images.jianshu.io/upload_images/14383117-7d9bf2097fa88b37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **04:00** 字符串需要加引号，一般情况下单双引号没有区别

- **04:56** 向量→矩阵

  ```r
  > a=1:10
  > dim(a)=c(2,5)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-853d5db15a55cb16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **06：26** `pheatmap::pheatmap(a)`

  ![](https://upload-images.jianshu.io/upload_images/14383117-9dadd2ec4c281115.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **06:36** 赋值

  ```r
  > a[1,2]='5'
  ```

  ps. `'5'`为character

  导致

  ![](https://upload-images.jianshu.io/upload_images/14383117-78bcef280fda6a93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  此时则不能生成热图

  ![](https://upload-images.jianshu.io/upload_images/14383117-922fa1d020878b38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **07:09** 判断工作对象 (object) 的性质

  ```R
  > class(a)
  > str(a)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-482dbe499f41cb96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **09:11** 判断 obejct 是否是 `matrix` 或其他

  ```R
  > is.matrix(a)
  > is.data.frame(a)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-a9b1ce537ef35ae1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- **11:12** 将 object 转换为 `data.frame` 格式

  ```r
  > as.data.frame(a)
  > b=as.data.frame(a)
  > b[1,2]='5'
  > pheatmap::pheatmap(b)
  ```

  > Error in cut.default(x, breaks = breaks, include.lowest = T) : 
  >   'x' must be numeric

  ![](https://upload-images.jianshu.io/upload_images/14383117-7c6b4fa613fcbab4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  要保证执行操作前输入数据的性质符合函数要求。

- **17:04** `$`可查看数据框中的列

  ```r
  > d=options()
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-1e6bc19eb11e47f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-812f6cb06f55751d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **18:39** 查看 `d$repos`

   ``` R
  > e=d$repos
  > class(e)
  > mode(e)
  > typeof(e)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-18f8df81fe78a6d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-52c38ffa7d7c29f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **19:16** 查看 d 中每一个元素的长度

  ```R
  > lapply(d,length)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-1bd429dfbcdf9954.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  d 中一共有87个元素，这样查看很不直观/方便。

- **19:56** 将 d 变为非 list 数据，查看每个元素的长度

  ```R
  > unlist(lapply(d,length)) 
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-10502b0a333f0262.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **20:19** 只查看长度，不显示名称

  ```R
  > as.numeric(unlist(lapply(d,length)))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-5048c4c0ab63625d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **24:15** 学习在数据框中抓取元素（取子集）的[数据准备](<https://www.ncbi.nlm.nih.gov/Traces/study/?uids=5172808%2C5172807%2C5172806%2C5172805%2C5172803%2C5172804%2C5172802%2C5172801%2C5172800%2C5172799%2C5172798%2C5172797%2C5172796%2C5172794%2C5172795%2C5172792%2C5172793%2C5172791%2C5172790%2C5172789%2C5172788%2C5172787%2C5172786%2C5172785>)

  ```R
  > a=read.table('YourPath/SraRunTable.txt',stringsAsFactors=F,header=T,sep='\t')
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-62c2f10f15c1f4f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-9bf0d58f18bad4f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **24:16** 查看 a 的性质

  ![](https://upload-images.jianshu.io/upload_images/14383117-ca92813b0dfd6ffb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **24:58** 任意取几行数据成为一个 `temp`, 以 ‘Assy_Type’ 为 ‘RNA-Seq’ 为搜索项

  > 不过这份数据里都是 RNA-Seq, 不是非常能体现意义

  ``` R
  > temp=a[c(1,2,7),]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-53e703a0f519537d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-4d2591a10a60a6e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **25:25** 上面的方法需要用肉眼判断每个元素的坐标，而采取 TRUE or FALSE 的方法更高效

  ```r
  > grep('RNA-Seq',a$Assay_Type)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-997f2cc9d2a0cb3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  > 取到了 'Assay_Type' 为  ‘RNA-Seq’  的所有元素的下标

  ```r
  > grepl('RNA-Seq',a$Assay_Type)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-352b3dc68aae5c66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  > 判断每个元素的 'Assay_Type' 是否为  ‘RNA-Seq’  

- **28:00** 两种方法核对

  ```R
  > index1=grep('RNA-Seq',a$Assay_Type)
  > index2=grepl('RNA-Seq',a$Assay_Type)
  > b=a[index1,]
  > b=a[index2,]
  ```

- **29:11** 两种取子集方法在 list 中的应用

  > 接着之前的 `d=options()`
  >
  > ![](https://upload-images.jianshu.io/upload_images/14383117-cadf0ef93d03bacc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > as.numeric(unlist(lapply(d,length))) > 2
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-5d6eba10ae5b1439.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > table(as.numeric(unlist(lapply(d,length))) > 2)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-7a95bd2c98b7bbfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > index3=as.numeric(unlist(lapply(d,length))) > 2
  > d=d[index3]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-36892461b0b8165e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  > list 是层层叠加的，所以在 `d=d[index3]` 中没有代表“行、列”的逗号。

- **30:47** 验证 list 的层层叠加

  ```R
  > d[4]
  > class(d[4])
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-bd08d9c08dc4ec47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
  

## P3 外部数据导入导出

- **02:12** 直接导入

  ![](https://upload-images.jianshu.io/upload_images/14383117-aaab20136266356a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **02:37** `read.table()` 读取表格文件

  参数中：

  `sep='\t'`，以制表符为分隔符

  `header=T`，设第一行为表头（第一行比其他行少一列时）

  ```R
  > a=read.table('SraRunTable.txt',sep='\t',header=T)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-6fa94f1e800725eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-e7b6ff326d3cfd5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-f27360032fc758d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-e70f261c669acc28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![image](https://upload-images.jianshu.io/upload_images/14383117-1ef6e32e0471abf9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

  `comment.char=''`，' ' 后的内容不读取

  ```R
  > b=read.table('GSE17215_series_matrix.txt.gz',sep = '\t',comment.char='!',header = T)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-1fafe4ab1af3d26a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-e8d43b32bfc35f75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-c7d12725bd4b1564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-4a777bce8a616335.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **08:53** `write.csv()` 创建/另存为 csv 文件

  ```R
  > write.csv(b,'GSE17215_series_matrix.csv')
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-73a31fa4489163d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **11:08** `rownames()=[,1]`，第一列设为行名

  ​           `[,-1]` ，去掉行名

  ```R
  > rownames(b)=b[,1]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-32dfd29cdd4d5634.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-ee1948308f1e6b7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > b=b[,-1]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-9953c2d56f1075da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **12:05** 随机取数据绘制热图

  ```R
  > pheatmap::pheatmap(b[1:10,])
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-13910ab857d6e251.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  以log<sub>2</sub>值呈现

  ```R
  > b=log2(b)
  > pheatmap::pheatmap(b[1:10,])
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-898c4b8ea7c1fc89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **12:44** 储存为 Rdata 格式

  ```R
  > save(b,file = 'b_input.Rdata')
  ```



## P4 中级变量操作

- **02:36** 去行名

  ![](https://upload-images.jianshu.io/upload_images/14383117-fd4da86dad23419f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > write.csv(b,'GSE17215_series_matrix.csv',row.names = F)
  ```

- **05:08** 筛选

  ![](https://upload-images.jianshu.io/upload_images/14383117-0ca2c4002575b15c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > sort(a$MBases)
  > sort(a$MBases)[1]
  > sort(a$MBases,decreasing = T)[1]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-5a89b8b5c783a049.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  > `[1]`, 为第一个/最小值
  > `decreasing = T`，从大到小

- **06:44** 最大值、最小值、五数概括法（最小值；第1四分位数(Q1)；中位数(Q2)；第3四分位数(Q3)；最大值）

  ```R
  > max(a$MBases)
  > min(a$MBases)
  > fivenum(a$MBases)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-4b0299535d57e63d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **08:17** 筛选

  ```R
  > a$MBases > 5
  > a$MBases < 5
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-a9139fcd79e7a260.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  查看筛选结果的数量

  ```R
  > table(a$MBases < 5)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-6fbc8a06b6b6c2dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **09:51** 根据 `Assay_Type` 分组

  ```R
  > boxplot(a$MBases~a$Assay_Type)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-28e8e1ac0878f787.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  > 这份数据里只有 'RNA-Seq' 一种 Assay_type

- **11:56** 分别取 `fivenum()` （有不同 Assay_type 的情况下）

  ```R
  > wes=a[a$Assay_type=='WXS']
  > rna=a[a$Assay_type=='RNA-Seq']
  > fivenum(wes$MBases)
  > fivenum(rna$MBases)
  ```

- **16:19** 取行平均值

  ```R
  > as.numeric(b[1,])
  > rowMeans(b)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-cfd2b0374f1c6e8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **16:52** 引入循环

  `for` 循环，得到所有行的平均值

  ```R
  > for (i in 1:nrow(b)) {
      print(mean(as.numeric(b[i,])))  
  }
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-68cb8779a9f99136.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  `apply` 循环，得到所有行的平均值

  ```R
  > x=as.numeric(b[1,])
  > apply(b,1,function(x){mean(x)})
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-47b28baa1179a512.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  `for` 循环，得到所有行的最大值

  ```R
  > for (i in 1:nrow(b)) {
      print(max(as.numeric(b[i,])))  
  }
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-f8285f154b26212a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  `apply` 循环，得到所有行的最大值

  ```R
  > apply(b,1,function(x){max(x)})
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-8c0277aafa455d8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **25:05** 任意设置一个函数

  ```R
  > testf <- function(b){
    for(i in 1:nrow(b)){
      x=as.numeric(b[i,])
      y=x[1]+x[2]-x[3]+x[4]-x[5]+x[6]
      print(y)
    }
  }
  > testf(b)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-bc7bb4ec4bc4c555.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **26:07** 求方差

  求每一行的方差

  ```R
  > apply(b,1,sd)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-1407ecff99dd2014.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  方差最大的前50个探针

  ```R
  > sort(apply(b,1,sd),decreasing = T)[1:50]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-1ae0742b09561b8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  得到这些探针的名字，绘制热图

  ```R
  > cg=names(sort(apply(b,1,sd),decreasing = T)[1:50])
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-be7b0999ebf90a5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > pheatmap::pheatmap(b[cg,])
  ```

  

  ![](https://upload-images.jianshu.io/upload_images/14383117-f0e4890366d06681.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P5 热图

- **01:18** 直接用矩阵画图

  ```R
  > a1=rnorm(100)
  > dim(a1)=c(5,20)
  > library(pheatmap)
  > pheatmap(a1)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-87485876810b13c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > a2=rnorm(100)+2
  > dim(a2)=c(5,20)
  > pheatmap(a2)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-d2979d1c1e669af8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **02:25** 将 a1, a2 两个矩阵绘制在同一副热图

  ```R
  > pheatmap(cbind(a1,a2))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-8e5927687875c00e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > pheatmap(cbind(a1,a2),cluster_cols = F)
  ```

  > `cluster_cols = F`，不排序

  ![](https://upload-images.jianshu.io/upload_images/14383117-410b07268157031b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:50** 转换为 `data.frame` 格式，添加列名

  ```R
  > b=cbind(a1,a2)
  > b=as.data.frame(b)
  > c(paste('a1',1:20,sep = '_'),paste('a2',1:20,sep = '_'))
  > names(b)=c(paste('a1',1:20,sep = '_'),paste('a2',1:20,sep = '_'))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-de82cbd8cd0a1e63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-5035cb7c0e39d1e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > pheatmap(b,cluster_cols = F)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-362d8aa9d92f7524.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **12:57** 加上 `annotation_col` 参数

    ```R
  > tmp=data.frame(group=c(rep('a1',20),rep('a2',20)))
  ```

  >`rep()`，重复
  >
  >![](https://upload-images.jianshu.io/upload_images/14383117-683579a12968cef6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-500b8a860237e4b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

  给 tmp 加上列名，画图

  ```R
  > row.names(tmp)=colnames(b)
  > pheatmap(b,annotation_col = tmp)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-c8bd75c3436be226.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## P6 选取差异明显的基因的表达量矩阵绘制热图

- **01:00** 选1000个基因绘制热图

  ```R
  > cg=names(tail(sort(apply(dat, 1, sd)),1000))
  > library(pheatmap)
  > pheatmap(dat[cg,],show_colnames = F,show_rownames = F)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-da705039113f648b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **01:56** 归一化

  ```R
  > n=t(scale(t(dat[cg,])))
  > n[n>2]=2
  > n[n< -2]= -2
  > n[1:4,1:4]
  > pheatmap(n,show_colnames = F,show_rownames = F)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-88420f4136d171ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  加注释

  ```R
  > ac=data.frame(g=group_list)
  > rownames(ac)=colnames(n)
  > pheatmap(n,show_colnames = F,show_rownames = F,annotation_col = ac)
  ```




## P7 ID转换

> 数据准备：[Ensembl gene ID](https://useast.ensembl.org/biomart/martview) （似乎需要科学上网）
>
> ![](https://upload-images.jianshu.io/upload_images/14383117-bd927ef38e9d7af1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>
> ![](https://upload-images.jianshu.io/upload_images/14383117-f1c59d3401e23c7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **01:48** 读入

  ```R
  > options(stringsAsFactors = F)
  > a=read.table('ensembl.txt')
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-2faa7090e0bc90e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **02:56** 取 ‘.’ 号之前的部分（基因名）

  ```R
  > strsplit('ENSG00000000003.14','[.]')
  > strsplit('ENSG00000000003.14','[.]')[[1]]
  > strsplit('ENSG00000000003.14','[.]')[[1]][1]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-9578182910f53f0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:48** 批量取基因名

  ```R
  > library(stringr)
  > str_split(a$V1,'[.]')
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-b00e1842a0f4b0b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > unlist(str_split(a$V1,'[.]'))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-316d2e2fd80f3fb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  这一步得到的是 `character`

  ![](https://upload-images.jianshu.io/upload_images/14383117-dfec8936c7568b05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  所以需要在之前的代码中增加一个参数

  ```R
  > unlist(str_split(a$V1,'[.]',simplify = T))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-88b866403b1bead9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **05:13** 批量取第一列（基因名）

  ```R
  > a$ensembl_id=str_split(a$V1,'[.]',simplify = T)[,1]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-fb8de8348d4c4b5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **05:50** 转换

  ```R
  > library(org.Hs.eg.db)
  > g2s=toTable(org.Hs.egSYMBOL)
  > g2e=toTable(org.Hs.egENSEMBL)
  ```

- **07:15** 关联

  ```R
  > b=merge(a,g2e,by='ensembl_id',all.x=T)
  > d=merge(b,g2s,by='gene_id',all.x=T)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-aee3d86ff31b54f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-509d142c89739c4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **08:46** 调序，调成和 ensembl.txt 中一致的顺序

  搜索 d 中 ensembl_id 重复的基因

  ```R
  > table(table(d$ensembl_id)>1)
  > table(d$ensembl_id)[table(d$ensembl_id)>1]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-c127bc5222b9b400.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  去重，调序

  ```R
  > d=d[order(d$V1),]
  > d=d[!duplicated(d$V1),]
  > d=d[match(a$V1,d$V1),]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-dcca9979c5447453.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-a0f50b9d9d133005.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P8 任意基因任意癌症表达量分组的生存分析

- **02:19** 读取

  ```R
  > options(stringsAsFactors = F)
  > a=read.table('LGG_93663_50_50.csv',header = T,sep = ',',fill = T)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-70d2018b6ca57606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:23** `ggbetweenstats` 作图

  ```R
  > library(ggstatsplot)
  > ggbetweenstats(data = dat,x=Group,y=Expression)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-4f89f9f293ceac6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **05:19** 生存曲线绘制

  ```R
  > library(ggplot2)
  > library(survival)
  > library(survminer)
  > table(dat$Status)
  > dat$Status=ifelse(dat$Status=='Dead',1,0)
  > sfit <- survfit(Surv(Days,Status)~Group,data=dat)
  > sfit
  > summary(sfit)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-cb6f151caf5b9b3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > ggsurvplot(sfit,conf.int = F,pval = TRUE)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-6d961382f23807e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **06:09** 调整图片

  ```R
  > ggsurvplot(sfit,palette = c("#E7B800","#2E9FDF"),
  + risk.table=TRUE,xlab="Time in months",
  + ggthemes=theme_light(),
  + ncensor.plot=TRUE)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-8198700ab5b4f1dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P9 任意基因任意癌症表达量和临床形状关联

- **04:35** 读取文件，更改列名

  ```R
  > a=read.table('plot-data-ARHGAP18-TCGA-0V-cbioportal.txt',header = T,sep = '\t',fill = T)
  > colnames(a)=c('id','stage','gene','mut')
  ```

- **05:37** `ggbetweenstats` 作图

  ```R
  > library(ggstatsplot)
  > ggbetweenstats(data=dat,x=stage,y=gene)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-5b2ce6a4a30c4922.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P10 表达矩阵的样本的相关性

- **02:05** 从 `airway` 获得表达矩阵

  ```R
  > library(airway)
  > data(airway)
  > exprSet=assay(airway)
  ```

  查看维度

  ```R
  > dim(exprSet)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-2130cbe0e881b395.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:07** 查看样本相关性，绘制热图

  ```R
  > cor(exprSet[,1],exprSet[,2])
  > cor(exprSet)
  > pheatmap::pheatmap(cor(exprSet))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-55a47985f78af647.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-e195fe2cd37c4a78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **05:42** 给热图添加注释

  ```R
  > group_list=colData(airway)[,3]
  > tmp=data.frame(g=group_list)
  > rownames(tmp)=colnames(cor(exprSet))
  > pheatmap::pheatmap(cor(exprSet),annotation_col = tmp)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-a538d1f6d4fc9c1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **07:33** 数据筛选

  ![](https://upload-images.jianshu.io/upload_images/14383117-39560c5da3c02bd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  > 显然第2行的数据不可用
  >
  > ```R
  > > x=exprSet[1,]
  > > table(x>1)
  > > sum(x>1)
  > > sum(x>1)>5
  > > x=exprSet[2,]
  > > sum(x>1)>5
  > ```
  >
  > ![](https://upload-images.jianshu.io/upload_images/14383117-836787d5e50e36ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  >
  > 通过以上代码找到判断方法

  ```R
  > apply(exprSet, 1,function(x) sum(x>1)>5)
  > exprSet=exprSet[apply(exprSet, 1,function(x) sum(x>1)>5),]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-a84f00ad53a1df36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-9d8ab808dcb94930.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **10:24** 进一步缩小表达矩阵

  ```R
  > exprSet=log(edgeR::cpm(exprSet)+1)
  > exprSet=exprSet[names(sort(apply(exprSet, 1, mad),decreasing = T)[1:500]),]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-a3ecd0cbff0a7b9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **11:07** 得到新的相关性矩阵，重新绘制热图

  ```R
  > M=cor(log2(exprSet+1))
  > pheatmap::pheatmap(M,annotation_col = tmp)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-664513a4e89d2057.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-fe7301dbbfc5de24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P11 芯片表达矩阵下游分析

- **00:46** 获得一个表达矩阵

  ```R
  > suppressPackageStartupMessages(library(CLL))
  > data(sCLLex)
  > exprSet=exprs(sCLLex)
  > samples=sampleNames(sCLLex)
  > pdata=pData(sCLLex)
  > group_list=as.character(pdata[,2])
  > dim(exprSet)
  > exprSet[1:5,1:5]
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-0a2cf9771de73c65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > boxplot(exprSet)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-5765b244fdf5b92b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:21** 单个差异分析

  ```R
  > boxplot((exprSet[1,]~group_list))
  > t.test(exprSet[1,]~group_list)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-baf2d7c04d3f6d27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-44354d3329657aad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **04:01** 使用 `limma` 进行批量差异分析

  设计矩阵

  ```R
  > suppressMessages(library(limma))
  > design <- model.matrix(~0+factor(group_list))
  > colnames(design)=levels(factor(group_list))
  > rownames(design)=colnames(exprSet)
  > design
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-3cbf7e96cd4ef0f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  构造分组

  ```R
  > contrast.matrix <- makeContrasts(paste0(unique(group_list),collapse = "-"),levels = design)
  > contrast.matrix
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-0aa7caf9ba53a7c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  差异分析

  ```R
  > fit <- lmFit(exprSet,design)
  > fit2 <- contrasts.fit(fit,contrast.matrix)
  > fit2 <- eBayes(fit2)
  > tempOutput=topTable(fit2,coef = 1,n=Inf)
  > nrDEG=na.omit(tempOutput)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-9adfa88b594e2c4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  

## P12 RNA-Seq 表达矩阵差异分析

- **00:39** 加载表达矩阵

  ```R
  > options(stringsAsFactors = F)
  > library(airway)
  > data(airway)
  > exprSet=assay(airway)
  > colnames(exprSet)
  > group_list=colData(airway)[,3]
  > exprSet=exprSet[apply(exprSet, 1, function(x) sum(x>1)   >5 ),]
  > table(group_list)
  > exprSet[1:4,1:4]
  > boxplot(log(exprSet+1))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-8c94fd0222a67aaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-10b8dd32d2aed55c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **03:11** 使用 `DESeq2` 进行差异分析

  ```R
  > if(T){
  +   library(DESeq2)
  +   
  +   (colData <- data.frame(row.names = colnames(exprSet),
  +                          group_list=group_list))
  +   dds <- DESeqDataSetFromMatrix(countData=exprSet,
  +                                 colData=colData,
  +                                 design=~group_list)
  +   tmp_f='airway_DESseq2-dds.Rdata'}
  >   if(!file.exists(tmp_f)){
  +     dds <- DESeq(dds)
  +     save(dds,file = tmp_f)
  +   }
  estimating size factors
  estimating dispersions
  gene-wise dispersion estimates
  mean-dispersion relationship
  final dispersion estimates
  fitting model and testing
  >   load(file = tmp_f)
  >   res <- results(dds,
  +                  contrast=c("group_list","trt","untrt"))
  >   resOrdered <- res[order(res$padj),]
  >   head(resOrdered)
  >   DEG=as.data.frame(resOrdered)
  >   DESeq2_DEG=na.omit(DEG)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-eddc248725f433fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ![](https://upload-images.jianshu.io/upload_images/14383117-4f30e8223632a088.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **04:32** 下游分析

  ```R
  > nrDEG=DESeq2_DEG[,c(2,6)]
  > colnames(nrDEG)=c('logFC','P.Value')
  > attach(nrDEG)
  > plot(logFC,-log10(P.Value))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-a6a9382a699074b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```R
  > library(ggpubr)
  > df=nrDEG
  > df$v= -log10(P.Value)
  > ggscatter(df,x="logFC",y="v",size = 0.5)
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-31903c13696a6798.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  更改设置

  ```R
  > df$g=ifelse(df$P.Value>0.01,'stable',
              ifelse(df$logFC > 1.5,'up',
                     ifelse(df$logFC < -1.5,'down','stable')))
  > table(df$g)
  > df$name=rownames(df)
  > ggscatter(df,x="logFC",y="v",size = 0.5,color = 'g',
            palette = c("#00AF88","#E7B800","#FC4E07"))
  ```

  ![](https://upload-images.jianshu.io/upload_images/14383117-aca895c8c6613a2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--- 
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)

