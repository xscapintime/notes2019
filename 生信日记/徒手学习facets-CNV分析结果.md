# Copy number variation analysis  - `facets`

`facets` 是存储在 GitHub 上的R包。

`factes` 的开发基于 ASCN(allele-specific copy number) 分析，适用于全基因组测序(WGS)及全外显子测序(WES)。

`facets` 整合了BAM文件预处理、总信号和等位基因特异信号的分层联合处理，最终整合计入肿瘤纯度 (tumor purity)、倍性 (ploidy)、克隆异质性 (clonal heterogeneity) 的计算，并将结果可视化。

## Input

### Option 1: BAM & VCF input

> - A bam file of the **tumour** sample
> - A bam file of the **normal** sample (typically, a blood sample from the same patient)
> - A VCF file of common, polymorphic SNPs. 

### Option 2: Pileup input

以 `Option 1` 的 BAM 文件为对象，运行脚本 `cnv_facets.R` ，得到一个以逗号分隔的文件，包含参考基因组的读数及多态SNP的等位基因。一般有以下几列：

> - *Chromosome* Chromosome of the SNP
> - *Position* Position of the SNP
> - *File1R* Read depth supporting the REF allele in **normal** sample
> - *File1A* Read depth supporting the ALT allele in **normal** sample
> - *File2R* Read depth supporting the REF allele in **tumour** sample
> - *File2A* Read depth supporting the ALT allele in **tumour** sample

## Output

### CNV profile plot

主要看这张图。

依然直接拿 Boss Jimmy 给的文件看一眼。

![](https://upload-images.jianshu.io/upload_images/14383117-89779ed8092f1453.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> - The top panel displays total copy number log-ratio (logR), 
> - and the second panel displays allele-specific log-odds-ratio data (logOR) with chromosomes alternating in blue and gray. 
> - The third panel plots the corresponding integer (total, minor) copy number calls. 

### 除此之外还有

1. VCF file compressed and indexed of copy number variants.

   | Tag               | Type    | Description                                                  |
   | ----------------- | ------- | ------------------------------------------------------------ |
   | SVTYPE            | String  | Type of structural variant                                   |
   | SVLEN             | Integer | Difference in length between REF and ALT alleles             |
   | END               | Integer | End position of the variant described in this record         |
   | NUM_MARK          | Integer | Number of SNPs in the segment                                |
   | NHET              | Integer | Number of SNPs that are deemed heterozygous                  |
   | CNLR_MEDIAN       | Float   | Median log-ratio (logR) of the segment. logR is defined by the log-ratio of total read depth in the tumor versus that in the normal |
   | CNLR_MEDIAN_CLUST | Float   | Median log-ratio (logR) of the segment cluster. logR is defined by the log-ratio of total read depth in the tumor versus that in the normal |
   | MAF_R             | Float   | Log-odds-ratio (logOR) summary for the segment. logOR is defined by the log-odds ratio of the variant allele count in the tumor versus in the normal |
   | MAF_R_CLUST       | Float   | Log-odds-ratio (logOR) summary for the segment cluster. logOR is defined by the log-odds ratio of the variant allele count in the tumor versus that in the normal |
   | SEGCLUST          | Integer | Segment cluster to which the segment belongs                 |
   | CF_EM             | Float   | Cellular fraction, fraction of DNA associated with the aberrant genotype. Set to 1 for normal diploid |
   | TCN_EM            | Integer | Total copy number. 2 for normal diploid                      |
   | LCN_EM            | Integer | Lesser (minor) copy number. 1 for normal diploid             |
   | CNV_ANN           | String  | Annotation features assigned to this CNV                     |

2. Histograms of depth of coverage

   > Histograms of the distribution of read depth (coverage) across all the position in the tumour and normal sample, before and after filtering positions.
   >
   > ![](https://upload-images.jianshu.io/upload_images/14383117-7b438bc6ea340d15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. Diagnostic plot

   > This is a diagnostic plot to check how well the copy number fits work. For a good fit, the segment summaries should be close to one of the lines.
    ![](https://upload-images.jianshu.io/upload_images/14383117-16d06c97bde69f97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   > The copy-number log ratio that corresponds to a diploid segment is reported as the **dipLogR** value.
   >
   > <https://www.biostars.org/p/250557/>


4. Pileup file

   > File of nucleotide counts at each SNP in normal and tumour sample.

## Reference

- Shen R, Seshan VE. FACETS: allele-specific copy number and clonal heterogeneity analysis tool for high-throughput DNA sequencing. *Nucleic Acids Res*. 2016;44(16):e131. doi:10.1093/nar/gkw520
- Detect somatic copy number variants (CNV) in tumour-normal samples using the [facets](https://github.com/mskcc/facets) package <https://github.com/dariober/cnv_facets#input>

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
