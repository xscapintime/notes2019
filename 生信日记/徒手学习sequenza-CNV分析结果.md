# Copy number variation analysis  - `squenza`

## Sequenza CNV Analysis

`Sequenza` 包括在 `Python` 中实现的**2步**预处理步骤，及**第3步**在 `R` 中操作并得到：

- depth ratio 
- cellularity 
- ploidy 

最后将结果绘制成图。

### Step 1: Pre-processing – Walking the Genome 基因组步移

> The first pre-processing step is run on the final normal and tumour mapped data (BAM files) in order to walk the genome in a pileup format (automatically generated by `samtools`). 
>
> This first step **finds high quality sites** in the genomes and **extracts their depth and genotype** in the normal genome and **calculates the variant alleles and allele frequencies** in the tumour genome. 
>
> This output has one line for each position in the BAMs and includes information on the position, depths, allele frequencies, zygosity, GC in the location.

### Step 2: Perform Binning 数据合并

> The binning step takes the rows of genomic positions and **compresses** them down to 1 row for every 200 rows previously. This **massively reduces the file size** and processing time in the third step.

### Step 3: Running Sequenza Algorithms and Plotting Results
这一步用R实现。

---

## Sequenza Analysis Results and Visualisation

### Cellularity estimate

> One of the first and most important estimates that Sequenza provides is the **tumour cellularity**.

tumour cellularity 说明了肿瘤细胞在肿瘤基因组中的百分比，其分析是基于 BAF (B Allele Frequency) 及基因组的 depth ratio. 

由 `sequenza`  分析得到的 cellularity 结果一般是在 `*model_fit.pdf`.

直接拿jimmy大神给的结果文件看一眼，长这样：

![](https://upload-images.jianshu.io/upload_images/14383117-74116b2bbc385025.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- cellularity: 35%, 即肿瘤组织中细胞的占比

- ploidy: 4.2, 代表基因组被扩增的程度

  > ploidy: the amount of DNA per tumour cell, expressed as multiples of haploid genomes.
  >
  > https://www.crick.ac.uk/research/labs/peter-van-loo/software>
  
  > The ploidy of cancer cells refers to the amount of DNA they contain.
  >
  > - If there's a normal amount of DNA in the cells, they are said to be *diploid*. These cancers tend to grow and spread more slowly.
  > - If the amount of DNA is abnormal, then the cells are called *aneuploid*. These cancers tend to be more aggressive. (They tend to grow and spread faster.)
  >
  >  <https://www.cancer.org/cancer/breast-cancer/understanding-a-breast-cancer-diagnosis/ploidy-and-cell-proliferation.html>
- sd.BAF: 0.13, 即BAF的标准差，表示未发生大片段的杂合性丢失 ( long losses of heterozygosity).

  > **Loss of heterozygosity (LOH)** is a cross chromosomal event that results in loss of the entire gene and the surrounding chromosomal region.
  >
  > The **loss of heterozygosity** is a common occurrence in [cancer](https://en.wikipedia.org/wiki/Cancer), where it indicates the absence of a functional [tumor suppressor gene](https://en.wikipedia.org/wiki/Tumor_suppressor_gene) in the lost region. However, many people remain healthy with such a loss, because there still is one functional gene left on the other chromosome of the [chromosome pair](https://en.wikipedia.org/wiki/Chromosome_pair). The remaining copy of the tumor suppressor gene can be inactivated by a [point mutation](https://en.wikipedia.org/wiki/Point_mutation), leaving no tumor suppressor gene to protect the body. Loss of heterozygosity does not imply a homozygous state (which would require the presence of two identical alleles in the cell).
  >
  > <https://en.wikipedia.org/wiki/Loss_of_heterozygosity#Breast_Cancer_and_BRCA1/2>
### CNV inferences

`*genome_view.pdf` 文件查看基因组拷贝数，推断CNV.

![](https://upload-images.jianshu.io/upload_images/14383117-ce38d9f8721d661e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> This file contains three “pages” of copy number events through the entire genomic block.
>
> 1. The first page shows copy numbers of the A (red) and B (blue) alleles,
> 2. The second page shows overall copy number changes, and
> 3. The third page shows the B allele frequency and depth ratio through genomic block.

以单个染色体为单位查看：

![](https://upload-images.jianshu.io/upload_images/14383117-2fd89132fa71be46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

中间的gap为染色体着丝粒部分，因为难以测序，最终一般以短读长的gap出现。

以及 cellularity/ploidy 等高线图：

![](https://upload-images.jianshu.io/upload_images/14383117-ca72359890f93778.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据这些结果可对 CNV envent 进行预测和判断。

## CNV Visualisation/Confirmation in IGV

`*segments.txt` 可查看所预测的CNV坐标。

![](https://upload-images.jianshu.io/upload_images/14383117-567b3435e5839026.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后可以将BAM文件拖入IGV进行查看。
那为什么不拖入查看呢？因为我没有这些BAM文件。
![](https://upload-images.jianshu.io/upload_images/14383117-c12e1c73519191c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Reference

- **Cancer Genomics Modules/Copy Number Variation Workshop** <https://bpa-csiro-workshops.github.io/btp-manuals-md/modules/cancer-module-cnv/cnv-tut/#sequenza-cnv-analysis>
- **Introduction to DNA-Seq processing for cancer data - CNVs** <https://github.com/mbourgey/EBI_cancer_workshop_CNV>

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)

