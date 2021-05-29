# Dragon star Day 1

> [Dragonstar2019 by Kai Wang](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FWGLab%2Fdragonstar2019)
>
> 1. Genomic technologies in disease studies
> 2. **NGS data formats and variant calling**

[TOC]

## Part Ⅱ NGS data formats and variant calling

### 1 Basic Concepts in NGS

- Insert – the DNA portion that is used for sequencing *note: 和突变中的insertion不同

  > Insert size is the length of the DNA (or RNA) that you want to sequence and that is "inserted" between the adapters (so adapters excluded).
  >
  > <https://www.biostars.org/p/95803/>

- Read – the part of the insert that is sequenced 读长

- Single End – a sequencing procedure by which the insert is sequenced from one end only 单端

- Paired End – a sequencing procedure by which the insert is sequenced from both ends 双端

### 2 Data formats

#### 2.1 The Rawset of Raw Data

**Typically: images** 根据不同碱基发射出不同颜色的荧光

![](https://s2.ax1x.com/2019/08/06/e4iBMn.png)

**base calling**:  call nucleotides at each base of each read.

#### 2.2 NGS data formats

**FASTQ**: The raw sequence data format

Millions of short reads from unknown genetic locations.

![](https://s2.ax1x.com/2019/08/06/e4FpeP.png)

- Basic Qualities: a "Phred" scale

  *BQ* = -10log~10~(ε) where ε is the probability of an error.
  
  > Phred: *Phil's revised editor* by Phil Green
  >
  > https://www.pnas.org/content/101/39/13991>
  >
  
  > **Base quality conversion**
  >
  > Nowadays, we settled on using quality scores on the original Sanger format,(Phred+33).
  >
  > 需要知道测序年份/公司，来确定Phred
  >
  > ![](https://s2.ax1x.com/2019/07/31/eYAiUs.png)<https://en.wikipedia.org/wiki/FASTQ_format>
  
- **BED**: Genomic region format

  - The first 3 required BED fields are:
  
    1. **chrom**: "chr1"
  
    2. **chromStart**: "0"
  
    3. **chromEnd**: "3"
  
    只包含这三种信息的BED: Minimal BED format. So-called **BED3** format.
  
  - 再加上：
  
    4. **labels**: "foo", "bar", "biz"...
    5. **scores**: "3.1"
    6. **strands**: "+", "-"
  
    This is so-called **BED6** format.
  
- **GFF**: annotates one line per feature

- **BAM/SAM**: Genome alignment format

  - SAM: Sequence Alignment/Map format (tab-delimited text file). 
  - BAM: The **binary** equivalent of a SAM file, which stores the same data in a compressed binary representation

  ![https://www.samformat.info/sam-format-flag](https://s2.ax1x.com/2019/08/07/e5ojrd.png)

- **CRAM**

  和 BAM/SAM 类似的另一种格式，无损压缩，适用于大型人群基因组/外显子组测序项目，如 UK Biobank.

  - CRAM was designed to be an efficient reference-based **alternative** to SAM/BAM file formats
  - Better **lossless compression** than BAM, but also allow for controlled loss of BAM data

- **VCF**: Variant Call Format

  - A **gold** standard for describing variants.

  - One **locus** per line, and it may contain more than one mutations, but most lines contain **one variant only**.

  - 主要分为：

    - the header line

      包括：CHROM POS ID REF ALT QUAL FILTER INFO 

      存在genotype时，还有：FORMAT

    - the INFO line

      用分号分隔的keys, 用等号定义可选的值

    - the genotypes

      定义数据类型及顺序

- **gVCF** (Genomic VCF): 

  - the basic format specification is **the same as for a regular VCF**, but gVCF contains **extra information**.
  - gVCF储存了变异及非变异位点的测序信息，适用于临床分析

#### 2.3 Formats use different coordinate systems, which adds confusion

不同文件格式对染色体坐标的起始位置定义不同，造成了一定的困扰

> BED: 0-based, half-open
> GFF: 1-based, fully closed
> SAM: 1-based, fully closed
> BAM: 0-based, half-open
> VCF: 1-based, fully closed
> …

### 3 Visualization of genomic data: IGV

强大的IGV

**IGV**: a high-performance visualization tool for interactive exploration of large, integrated genomic datasets.

### 4 Coverage

#### 4.1 What is coverage?

- The **depth** of sequencing coverage can be defined theoretically as **LN/G**, where L is the **read length**, N is the **number of reads** and G is the **haploid genome length**.

  测序深度可理解为：(read长度/read数量)/单倍体基因组长度

- The **breadth** of coverage is the **percentage of target bases** that have been sequenced for a given number of times.

- The **accuracy** of variant calling is affected by sequence quality, uniformity of coverage and the threshold of false-discovery rate that is used.

  > ## What is variant calling?
  >
  > Variant calling is the process by which we identify variants from sequence data (Figure 11).
  >
  > 1. Carry out whole genome or whole exome sequencing to create **FASTQ** files.
  > 2. Align the sequences to a reference genome, creating **BAM** or **CRAM** files.
  > 3. Identify where the aligned reads differ from the reference genome and write to a **VCF** file.
  >
  > Identify where the aligned reads differ from the reference genome and write to a VCF file.
  >
  > ![](https://www.ebi.ac.uk/training/online/sites/ebi.ac.uk.training.online/files/GenVar_Fig_CRAM_file.png)
  >
  > Figure 11 A CRAM file aligned to a reference genomic region as visualised in Ensembl. Differences are highlighted in red in the reads, and will be called as variants.
  >
  > reads中标红的碱基为variants.
  >
  > <https://www.ebi.ac.uk/training/online/course/human-genetic-variation-i-introduction-2019/variant-identification-and-analysis>

#### 4.2 Coverage: how many reads we need to cover the genome?

- Depth of coverage model

  鸟枪法测序中，genome size ***G***, read size ***S***, ***N*** reads

  某个read出现在某个间隙(interval)的概率为：*p=L/G*

  ***D***: 出现在与read同等长度的间隙的read的数量

  *D* ~ Binomial(*N, L/G*)，D与N, p 符合二项分布

  即：**b(*x*; *n, P*) = ~n~C~x~* P^x^ * (1 - P) ^n-x^** 

  ​	   **b(*D*; *N,  L/G*) = ~N~C~D~* (L/G)^D^ * (1 - L/G) ^N-D^** 

  Let *L* = *S*, *D* is also the number of reads that cover the last position of the interval → ***D* is depth of coverage**.

  因为 *S << G*, *N*的值特别大，depth可近似理解为泊松分布：

  ***D ~ Poisson(λ)***
  *λ= SN/G* (average depth of coverage)

  $P(D=k)=\frac{e^{-\lambda}\lambda^k}{k!}$

- Fraction of genome that are covered

  根据上述公式可以计算：

  Given λ=40, the fraction of genome that are covered more than 30x (D>30) is: 0.938.

#### 4.3 Overdispersion

- Main cause of overdispersion

  **GC bias** and other technical factors lead to systematic bias in coverage, resulting in overdispersion.

- How to model overdispersion

  - Ideal situation (**Poisson distrition**): 
    $Var(D) = μ$

  - **Gamma-Poisson** is equivalent to Negative Binomial, which is a  commonly used model for dealing with overdispersion in count data: 
    $Var(D) = μ+ μ^2/k$

  - 过离散模型也适用于其他因素，如 biological noise

#### 4.4 Question on coverage

Why do we need average 30-50x in a typical WGS experiment, and 100x in WES?

> WGS is less biased compared to WES. We do not need as much depth to call a variant confidently. Check the following publications for more detailed information.
>
> " Exome-seq achieves 95% SNP detection sensitivity at a mean on-target depth of 40 reads, whereas WGS only requires a mean of 14 reads. "
>
> Article [Variant detection sensitivity and biases in whole genome and...](https://www.researchgate.net/publication/264053300_Variant_detection_sensitivity_and_biases_in_whole_genome_and_exome_sequencing)
>
> [Shu-Hong Lin](https://www.researchgate.net/profile/Shu_Hong_Lin)
>
> <https://www.researchgate.net/post/Why_is_NGS_coverage_of_seemingly_less_complex_exome_sequences_higher_than_that_of_whole_genome_sequencing>

"WGS is less biased compared to WES."的原因：WES的样品首先需要经过PCR

> ![](https://s2.ax1x.com/2019/08/07/eIJQhD.png)
>
> Why 30X WGS beats 100X WES for variant coverage
>
> <https://www.variantyx.com/variantyx-posts/why-30x-wgs-beats-100x-wes-for-variant-coverage/>

### 5 General strategy for variant calling

#### 5.1 Possible Genotypes

1. 当只有参考基因组时，各种情形的概率均为1

   ![](https://s2.ax1x.com/2019/08/07/eIodOJ.png)

   P(reads|A/A, read mapped) = P(C observed|A/A, read mapped) = 1.0

   P(reads|A/C, read mapped) = P(C observed|A/C, read mapped) = 1.0

   P(reads|C/C, read mapped) = P(C observed|C/C, read mapped) = 1.0

2. 假定error rate为0.01

   ![](https://s2.ax1x.com/2019/08/07/eI7mrQ.png)

3. 当第1条read的该位点为**C**时

   P(reads|A/A, read mapped) = 0.01

   P(reads|A/C, read mapped) = 0.5

   P(reads|C/C, read mapped) = 1 - 0.01 = 0.99

3. 当第2条read的该位点为**C**时

   P(reads|A/A, read mapped) = 0.01^2^ = 0.0001

   P(reads|A/C, read mapped) = 0.5^2^ = 0.25

   P(reads|C/C, read mapped) = 0.99^2^ = 0.9801

4. 当第3条read的该位点为**C**时

   P(reads|A/A, read mapped) = 0.01^3^ = 0.000001

   P(reads|A/C, read mapped) = 0.5^3^ = 0.125

   P(reads|C/C, read mapped) = 0.99^3^ = 0.970299

5. 当第4条read的该位点为**A**时

   P(reads|A/A, read mapped) = 0.01^3^ * 0.99 = 0.00000099

   P(reads|A/C, read mapped) = 0.5^4^ = 0.0625

   P(reads|C/C, read mapped) = 0.99^3^ * 0.01 = 0.00970299

6. 当第5条read的该位点为**A**

   P(reads|A/A, read mapped) = 0.01^3^ * 0.99^2^ = **0.00000098**

   P(reads|A/C, read mapped) = 0.5^5^ = **0.03125**

   P(reads|C/C, read mapped) = 0.99^3^ * 0.01^2^ = 0.0000970299 ≈ **0.000097**

总结出贝叶斯公式：

![](https://s2.ax1x.com/2019/08/07/eI782T.png)

Combine these **likelihoods** with a prior incorporating information from other individuals and flanking sites to **assign a genotype**. 

#### 5.2 From Sequence to Genotype: Individual Based Prior

**Individual Based Prior**: Evry site has 1/1000 probability of varying.

> Ingredients That Go Into Prior
>
> - Most sites don’t vary
>
>      P(non-reference base) ~ **0.001**
>
> - When a site does vary, it is usually heterozygous
>   P(non-reference heterozygote) ~ **0.001 * 2/3**
>   P(non-reference homozygote) ~ **0.001 * 1/3**
> - Mutation model
>   Transitions account for most variants (C↔T or A↔G)
>   Transversions account for minority of variants
>
> <https://pdfs.semanticscholar.org/0156/04c3ba76cf247a8010f74ec1386e58ceb530.pdf>

因此，各位点的先验概率为：

Prior(A/A) = 0.001 * 1/3 ≈  **0.00033**  0.00034?

Prior(A/C) = 0.001 * 2/3 ≈  **0.00067**  0.00066?

Prior(C/C) = 1 - 0.001 = **0.999**

琢磨了两个小时并把google搜索翻到了第十页，依然没看懂这里的后验概率是怎么算的，如果有统计大神看到这里，求赐教🙏

![](https://s2.ax1x.com/2019/08/07/eokvkt.png)

#### 5.3 From Sequence to Genotype: Population Based Prior

**Population Based Prior**: Use frequency information from examining others at the same site. *In the example above, we estimated P(A) = 0.20*

因此，各位点的先验概率为：

Prior(A/A) = 0.2 * 0.2 = **0.04**

Prior(A/C) = 1 - 0.04 - 0.64 = **0.32**

Prior(C/C) = 0.8 * 0.8 = **0.64**

同样的迷惑：

![](https://s2.ax1x.com/2019/08/07/eoAKcF.png)

#### 5.4 Sequence Based Genotype Calls

> - **Individual Based Prior**
>   - Assumes all sites have an equal probability of showing polymorphism
>   - Specifically, assumption is that about 1/1000 bases differ from reference
>   - If reads where error free and sampling Poisson …
>   - … 14x coverage would allow for 99.8% genotype accuracy
>   - … 30x coverage of the genome needed to allow for errors and clustering
> - **Population Based Prior**
>   - Uses frequency information obtained from examining other individuals
>   - Calling very rare polymorphisms still requires 20-30x coverage of the genome
>   - Calling common polymorphisms requires much less data
> 
><https://pdfs.semanticscholar.org/0156/04c3ba76cf247a8010f74ec1386e58ceb530.pdf>