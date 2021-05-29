> 大佬台上讲课一分钟，小🌶🐤课下google十小时

> [Dragonstar2019 by Kai Wang](https://github.com/WGLab/dragonstar2019)
1.Detection of structural variants in human 
2.Annotation and phenotype-driven interpretation of genetic variants


## Detection of structural variants in human 

### Human genetic variation

![Pollex et al, Circulation. 2007](https://upload-images.jianshu.io/upload_images/14383117-e94ab58e51dbea0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> ![image](https://upload-images.jianshu.io/upload_images/14383117-137f2d0738743621.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>
> <http://doc.goldenhelix.com/SVS/tutorials/cnv_univariate_analysis/overview.html>

### Mechanisms underlying structural variant formation

> ![image](https://upload-images.jianshu.io/upload_images/14383117-83f4cae035735d44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>
> Ottaviani D, LeCain M, Sheer D. The role of microhomology in genomic structural variation[J]. Trends in Genetics, 2014, 30(3): 85-94.

#### Recurrent structural variants

- Share the same size and genomic content in unrelated individuals.

- Often caused by **NAHR** (Nonallelic homologous recombination--Nonallelic pairing of paralogous sequences and crossover leading to deletions, duplications and inversions )

  非等位基因同源重组

  > **Non-allelic homologous recombination** (**NAHR**) is a form of [homologous recombination](https://en.wikipedia.org/wiki/Homologous_recombination) that occurs between two lengths of [DNA](https://en.wikipedia.org/wiki/DNA) that have high sequence similarity, but are not [alleles](https://en.wikipedia.org/wiki/Allele).
  >
  > <https://en.wikipedia.org/wiki/Non-allelic_homologous_recombination>

#### Nonrecurrent rearrangements

- Typical mechanisms:

  - NHEJ: Non-homologous end joining - 非同源性末端接合

    >**Nonhomologous end joining (NHEJ)** is an error-prone mechanism that does not use any DNA sequence homology to repair the DSB (Weterings & Chen, 2008).
    >
    >From: [Methods in Enzymology, 2017](https://www.sciencedirect.com/science/article/pii/S0076687917300927)

  - MMEJ: Microhomology-mediated end joining - 微同源介導的末端連接

    > **Microhomology-mediated end joining** (**MMEJ**), also known as **alternative nonhomologous end-joining** (**Alt-NHEJ**) is one of the pathways for repairing [double-strand breaks](https://en.wikipedia.org/wiki/Double-strand_break) in DNA. 
    >
    > <https://en.wikipedia.org/wiki/Microhomology-mediated_end_joining>

  - FoSTeS/MMBIR: microhomology-mediated break-induced replication

    > FoSTeS: fork stalling and template switching

  - SRS: Smaller complex rearrangements caused by **serial replication slippage**

### Technologies for CNV Detection

#### Karyotyping and cytogenetic analysis

- Giemsa staining

  > ![image](https://upload-images.jianshu.io/upload_images/14383117-02f032ddbc48afd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  - Giemsa banding (G-banding) - G显带
  - Dark bands are AT-rich and have less genes. 
  - Light bands are GC-rich DNA and are more transcriptionally active. 

- Fluorescent in situ hybridization (FISH) - 荧光原位杂交

  FISH uses **fluorescent probes** that bind to specific chromosomal 
  regions where there is a high degree of sequence complementarity.

- Comparative genomic hybridization (CGH) - 比较基因组杂交

  > **比较基因组杂交**（英语：Comparative genomic hybridization，**CGH**）是一种分子[细胞遗传学](https://zh.wikipedia.org/wiki/细胞遗传学)方法，在不培养[细胞](https://zh.wikipedia.org/wiki/细胞)的情况下，分析相对于参照样品，测试样品的DNA中[拷贝数变异](https://zh.wikipedia.org/w/index.php?title=拷贝数变异&action=edit&redlink=1)（CNV）的多倍性程度。其目的是快速有效地比较两个来源的两组DNA样本，这两组DNA通常是密切相关的，因为两者在整个[染色体](https://zh.wikipedia.org/wiki/染色体)或亚染色体区域（整个染色体的一部分）上都可能有获得或丢失。该技术最初是为了评估实体瘤和正常组织的染色体互补差异而开发的[[1\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Kallioniemi,Kallioniemi,Sudar,Rutovitz,Gray,Waldman,Pinkel-1)，相比更传统的[Giemsa显带](https://zh.wikipedia.org/wiki/G显带)技术和[荧光原位杂交](https://zh.wikipedia.org/wiki/荧光原位杂交)技术（FISH）（受限于所使用的[显微镜](https://zh.wikipedia.org/wiki/显微镜)分辨率），它的分辨率提高到了5-10Mb[[1\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Kallioniemi,Kallioniemi,Sudar,Rutovitz,Gray,Waldman,Pinkel-1)[[2\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Strachan-2)[[3\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Weiss,Hermsen,Meijer,VanGrieken,Baak,Kuipers,VanDiest-3)。
  >
  > https://zh.wikipedia.org/wiki/比较基因组杂交

- Spectral karyotyping (SKY)

  > **Spectral karyotyping (SKY)** is a FISH-based method that labels each chromosome with a different color, allowing the identification of the chromosomal origin of all elements of the examined karyotype.
  >
  > From: [Molecular Diagnostics, 2010](https://www.sciencedirect.com/science/article/pii/B9780123694287000215)

### SNP genotyping arrays

SNP genotyping array is a type of **DNA microarray** which is used to **detect SNPs**. 
- Affymetrix arrays
  - In the Affymetrix assay, there are **25-mer probes** for both  alleles. 
  - Assuming there are two alleles (e.g. A-Allele and B-allele) at a 
    particular site. 
    - The DNA can bind to **both probes**.
    - But will have much **higher affinity** for the perfectly matched probe.
- Illumina arrays
  - In the Illumina array, attached to each Illumina **bead** is a **50**-
    **mer** sequence complementary to the sequence adjacent to the SNP site. 
  - The single-base extension (T or G) that is **complementary to the allele** carried by the DNA (A or C, respectively) then binds and results in the appropriately-colored signal (red or green, 
    respectively). 

> ![Schematic view of SNP array analysis by Affymetrix (right) and Illumina (left). ](https://upload-images.jianshu.io/upload_images/14383117-fc23cdbe7f4cb635.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>
> Iacobucci I, Lonetti A, Papayannidis C, et al. Use of single nucleotide polymorphism array technology to improve the identification of chromosomal lesions in leukemia[J]. Current cancer drug targets, 2013, 13(7): 791-810.

### CNV Detection

#### Log R Ratio (LRR) and B Allele Frequency (BAF)

For both platforms, the computational algorithms convert the **raw signals** into **Log R Ratio (LRR)** and **B Allele Frequency (BAF)**. 

- **LRR** is a measure of normalized total signal intensity. 
- **BAF** is a measure of normalized allelic intensity ratio. 

> BAF = Y / (X + Y)
> LRR = log2( (X + Y)sampleOfInterest / (X+Y)baselineSample)
>
> <https://www.biostars.org/p/199025/>

#### Detection of CNVs from SNP arrays using *PennCNV*

- **Hidden Markov Model (HMM)** is a statistical Markov model in which the system being modeled is assumed to be a Markov process with hidden states.

  > **隐马尔可夫模型**（Hidden Markov Model；[缩写](https://zh.wikipedia.org/wiki/縮寫)：**HMM**）或称作**隐性马尔可夫模型**，是[统计](https://zh.wikipedia.org/wiki/统计)[模型](https://zh.wikipedia.org/wiki/模型)，它用来描述一个含有隐含未知参数的[马尔可夫过程](https://zh.wikipedia.org/wiki/马尔可夫过程)。其难点是从可观察的参数中确定该过程的隐含参数。然后利用这些参数来作进一步的分析，例如[模式识别](https://zh.wikipedia.org/wiki/模式识别)。
  >
  > 在**正常的**马尔可夫模型中，状态对于观察者来说是直接可见的。这样状态的转换概率便是全部的参数。而在**隐**马尔可夫模型中，状态并不是直接可见的，但受状态影响的某些变量则是可见的。每一个状态在可能输出的符号上都有一[概率分布](https://zh.wikipedia.org/wiki/概率分布)。因此输出符号的序列能够透露出状态序列的一些信息。
  >
  > https://zh.wikipedia.org/wiki/隐马尔可夫模型
  >
  > 延伸阅读：[漫谈 Hidden Markov Model](<http://freemind.pluskid.org/series/hmm-tutor/>)

- What we know are: LRR and BAF

- What we want to know is: **copy number**

Emission probability?

Transition probability?

LRR: 总的信号强度

SNP→CNV→genotype call

SNP array 效率低

#### eg. Joint Modeling on Family Data

Joint modeling of the CNVs in a trio
### SV

#### NGS-based SV detection

Structural Variants

特别复杂的SV需要 de novo assemble，contig比对到reference上

#### Read count-based methods for SV detection
#### Detection of SVs from discordant read pairs
- Pattern of inversions
- tandem duplication
- translocations

#### Detection of SVs using assembly-based methods

**De novo sequence assembly (AS)** enables the fine￾scale discovery of SVs, including novel (non-reference) sequence insertions

#### SV detection from long-read sequencing
#### Structural Variant Detection with Sniffles

second best alignment score is less than 2. 可能是100%一致

绝大多数SV可以通过short read seq找出来

#### Bionano optical mapping for SV detection
##### SV detection from Single-molecule optical mapping 

## Annotation and phenotype-driven interpretation of genetic variants
### ANNOVAR for variant annotation

#### ANNOVAR supports 3 types of annotations:

- Gene-based annotation (g)

  Several commonly used **gene definitions**:

  - RefSeq gene - NCBI
  - UCSC known gene - UCSC
  - ENSEMBL gene - Europe
  - GENCODE gene - a project, all 

  **precedence rules**

- Region-based annotation (r)

  Several commonly used **filter annotation**:

  - Cytogenetic band - Keyword is cytoBand

  - Located in a ChIP-Seq peaks from ENCODE - 很多国家的一个实验，通过不同的细胞系???

  - Located in a predicted repressor, promoter, enhancer, etc

    chromHMM predictions 预测非编码区?

    15 different “states” are provided

    把基因组分成了15种state

  - Overlap with conserved genomic regions

    - UCSC Genome Browser provides multi-species alignment for human 
      genome sequence
    - Conserved region may indicate functionally important regions

    *Selection of alignment*

- Filter-based annotation (f)

  Several commonly used filter annotation:

  - Allele frequency in various databases
  - Presence in various association databases
  - Functional prediction scores
  - dbSNP identifier

#### From functional annotation to clinical interpretation
-  5-tier system for clinical interpretation

- InterVar: Automated implementation of the ACMG-AMP clinical interpretation guidelines
### Introduction to HPO

The Human Phenotype Ontology (HPO) 

Our goal: from clinical phenotypes to genes

#### Phenolyzer

