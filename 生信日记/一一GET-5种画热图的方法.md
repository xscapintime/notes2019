> 这里依然需要一张meme
![](https://upload-images.jianshu.io/upload_images/14383117-c8b31911bb8d1cf8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前后文和数据来源见 [『生信技能树』R语言20题之我的答案版本](https://www.jianshu.com/p/62576e9ae9e3) 第13题。

## 1. `pheatmap`
```
> top50gene=tail(sort(madlist),50)
> top50gene=as.data.frame(top50gene)
> top50genelist=rownames(top50gene)
> top50matrix=uni_e[top50genelist,]
> ct=scale(top50matrix,center=T,scale=F)  ## 中心化
> nmlztop50matrix=scale(ct,center=T,scale=T) ## 标准化
```

```
> pheatmap::pheatmap(nmlztop50matrix)
```

![](https://upload-images.jianshu.io/upload_images/14383117-550424477ac0affa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2. `heatmap`
```
> heatmap(nmlztop50matrix)
```

![](https://upload-images.jianshu.io/upload_images/14383117-fe5c87baf42f7192.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3. `ggplot`
```
> ggplot(ml_nmtop50,aes(sample,symbol))+
+   geom_tile(aes(fill=value),colour = "white")+
+   scale_fill_gradient(low = "white",high = "steelblue")

```

![](https://upload-images.jianshu.io/upload_images/14383117-c310614ad38c86a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4. `gplots`
```
> library(gplots)
> heatmap.2(nmlztop50matrix,key = T,symkey = FALSE,density.info="none",trace="none")
```

![](https://upload-images.jianshu.io/upload_images/14383117-ec5881fe8710f1b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5. `lattice`, `latticeExtra`
```
> library(lattice)
> library(latticeExtra)
> x  <- t(as.matrix(scale(nmlztop50matrix)))
> dd.row <- as.dendrogram(hclust(dist(x)))
> row.ord <- order.dendrogram(dd.row)
> levelplot(t(nmlztop50matrix),aspect = "fill",xlab="sample",ylab="symbol",colorkey = list(space = "left"),legend=list(right=list(fun=dendrogramGrob,args=list(x=dd.row,rod=row.ord,side='right',size=5))))
> levelplot(t(nmlztop50matrix),aspect =
+             "fill",xlab="sample",ylab="symbol",colorkey =
+             list(space = "left"),legend=
+             list(right=list(fun=dendrogramGrob,args=
+                               list(x=dd.row,rod=row.ord,side='right',size=5))))
```

![](https://upload-images.jianshu.io/upload_images/14383117-22ceed934df433e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

