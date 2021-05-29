> ![](https://upload-images.jianshu.io/upload_images/14383117-c1040d162d322529.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

操作环境：Win10 Ubuntu子系统

## 1. 下载参考转录组

下载传送门：http://ftp.ensembl.org/pub/release-76/fasta/homo_sapiens/cdna/Homo_sapiens.GRCh38.cdna.all.fa.gz

由于 `wget` 下载速度太慢，用了神奇的Motrix软件分分钟成功下载到本地。

就是来自技能树推文的安利之一，推文传送门：https://mp.weixin.qq.com/s/whx-n1ktT58WHhWAUa06dw

![](https://upload-images.jianshu.io/upload_images/14383117-b67ec1b0a3793874.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
就是它👇
![](https://upload-images.jianshu.io/upload_images/14383117-300ad6ed74558d62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后直接将 `Homo_sapiens.GRCh38.cdna.all.fa.gz` 拖到子系统的工作路径下。

## 2. Salmon 构建 index

假设已经装好了 Miniconda.

### 2.1 在小环境中安装 Salmon

```shell
conda create --name rnaseq
conda activate rnaseq
conda install -y salmon
```

### 2.2 构建 index

#### 失败第一回

```shell
salmon index -t Homo_sapiens.GRCh38.cdna.all.fa.gz -i homo38_index
```

![](https://upload-images.jianshu.io/upload_images/14383117-7d31c614aa59aadb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`[Error reading from the FASTA/Q stream. Make sure the file is valid.]`

难道是文件不完整吗，抱着这种想法甚至重新下载了~~1~~n遍。

#### 失败第二回

看了别人构建好的 index, 里面有 `.json` 文件，难道是和网速有关？

开着战五渣的手机热点，依然 `[Error reading from the FASTA/Q stream. Make sure the file is valid.]` .

#### 失败最终回/成功第一回

查看文件详细信息：

```shell
ls -lh
```

![](https://upload-images.jianshu.io/upload_images/14383117-a58cd8800e9a0aea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**没有权限，毫无权限。**

![](https://upload-images.jianshu.io/upload_images/14383117-f74e748fc7ee151b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**那么就来修改权限🤦‍♂️**

```shell
chmod 777 Homo_sapiens.GRCh38.cdna.all.fa.gz
```

![](https://upload-images.jianshu.io/upload_images/14383117-e962d45a84b2293b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**这是坠吼的**

```shell
salmon index -t Homo_sapiens.GRCh38.cdna.all.fa.gz -i homo38_index
```

![](https://upload-images.jianshu.io/upload_images/14383117-9741778f2c3a08a3.gif?imageMogr2/auto-orient/strip)

## 所以构建 index 秒失败应该反思哪里呢

- 文件完整性，有必要
- 网速，阔以，么得必要
- 文件权限，非常必要，尤其用了除 `wget` 以外的~~骚~~操作下载

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
