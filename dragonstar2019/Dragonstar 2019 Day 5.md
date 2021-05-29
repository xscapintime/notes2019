# Dragonstar 2019 Day 5

## RNA-Seq in human diseases

### What can a transcriptome tell us?

### Technologies for Transcriptomics: 

#### expression microarrays:

- **Affymetrix GeneChip** is widely used before RNA-Seq is available

- **Illumina BeadChip** can also be used for gene expression analysis 

  使用率不高

#### RNA Sequencing (RNA-Seq):

**RNA-Seq** gradually replaced expression microarrays as the most 
widely used methods for gene expression analysis

#### Advantages of RNA-Seq over Microarrays

RNA-seq read count 动态范围大

chip测量荧光强度，动态范围小

RNA-seq可以直接和参考基因组比对

### Splice-aware RNA-Seq alignment methods

### Typical RNA-Seq considerations: 

- alignment
- count normalization
- sequencing biases

#### Selected RNA-Seq Alignment Programs

#### Normalization

- Quantification of Gene Expression
- RPKM/FPKM

- TPM (recommended)

  The reason is that the normalized count values output by the RPKM/FPKM method are not comparable between samples.

- Relationship btw TPM and FPKM

#### Differential Gene Expression Analysis

- Poisson Approximation to Binomial

  - Problem with Poisson: Over-dispersion

    var vs mean

- Accounting for Overdispersion

  - Linear models from microarray studies

    limma

  - count models using negative binomial (NB) 比泊松分布/二项分布更好

    edgeR, DESeq

    DESeq2: Empirical Bayes Shrinkage for Dispersion Estimation


#### Additional challenges: data biases

3‘ 5’ exon covarage 大幅降低

PCR 的 biases, GC高和低的区域有不同

- Available Methods for Bias Correction

#### Differential expression vs differential splicing、

case vs ck

有可能是基因表达本身的区别，也可能是splicing的区别

预测 异构体 Iterative assignment of reads to different isoforms by expectation maximization in **RSEM**

#### exome sequencing

找到以前被忽略的

*手机里拍的ppt (新加内容)

也许 splice mutation 是真正致病的

#### Tools for differential splicing analysis

#### A decade of single-cell RNA-seq



---

## Deep Learning in sequencing data analysis







