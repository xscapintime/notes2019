# Dragon star Day 3

> [Dragonstar2019 by Kai Wang](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FWGLab%2Fdragonstar2019)
>
> 1. Detection of structural variants in human 
> 2. Annotation and phenotype-driven interpretation of genetic variants

[TOC]

## Part Ⅰ Detection of structural variants in human 

### 1 Human genetic variation

![Pollex et al, Circulation. 2007](https://s2.ax1x.com/2019/08/01/eUcbp8.png)

> ![](https://s2.ax1x.com/2019/08/01/eaM5SU.png)
>
> <http://doc.goldenhelix.com/SVS/tutorials/cnv_univariate_analysis/overview.html>

### 2 Mechanisms underlying structural variant formation

> ![](https://s2.ax1x.com/2019/08/01/eUf4iR.png)
>
> Ottaviani D, LeCain M, Sheer D. The role of microhomology in genomic structural variation[J]. Trends in Genetics, 2014, 30(3): 85-94.

#### 2.1 Recurrent structural variants

- Share the same size and genomic content in unrelated individuals.

- Often caused by **NAHR** (Nonallelic homologous recombination--Nonallelic pairing of paralogous sequences and crossover leading to deletions, duplications and inversions )

  非等位基因同源重组

  > **Non-allelic homologous recombination** (**NAHR**) is a form of [homologous recombination](https://en.wikipedia.org/wiki/Homologous_recombination) that occurs between two lengths of [DNA](https://en.wikipedia.org/wiki/DNA) that have high sequence similarity, but are not [alleles](https://en.wikipedia.org/wiki/Allele).
  >
  > <https://en.wikipedia.org/wiki/Non-allelic_homologous_recombination>

#### 2.2 Nonrecurrent rearrangements

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

### 3 Technologies for CNV Detection

#### 3.1 Karyotyping and cytogenetic analysis

- Giemsa staining

  > ![](https://s2.ax1x.com/2019/08/01/eU4BvV.png)

  - Giemsa banding (G-banding) - G显带
  - Dark bands are AT-rich and have less genes. 
  - Light bands are GC-rich DNA and are more transcriptionally active. 

  > ![Huang et al, The Tohoku journal of experimental medicine. 2010](https://s2.ax1x.com/2019/08/12/exeqbj.png)

- Fluorescent in situ hybridization (FISH) - 荧光原位杂交

  FISH uses **fluorescent probes** that bind to specific chromosomal 
  regions where there is a high degree of sequence complementarity.\

  > ![Aleksic et al, Scientific reports. 2013](https://s2.ax1x.com/2019/08/12/exeXan.png)

- Comparative genomic hybridization (CGH) - 比较基因组杂交

  > **比较基因组杂交**（英语：Comparative genomic hybridization，**CGH**）是一种分子[细胞遗传学](https://zh.wikipedia.org/wiki/细胞遗传学)方法，在不培养[细胞](https://zh.wikipedia.org/wiki/细胞)的情况下，分析相对于参照样品，测试样品的DNA中[拷贝数变异](https://zh.wikipedia.org/w/index.php?title=拷贝数变异&action=edit&redlink=1)（CNV）的多倍性程度。其目的是快速有效地比较两个来源的两组DNA样本，这两组DNA通常是密切相关的，因为两者在整个[染色体](https://zh.wikipedia.org/wiki/染色体)或亚染色体区域（整个染色体的一部分）上都可能有获得或丢失。该技术最初是为了评估实体瘤和正常组织的染色体互补差异而开发的[[1\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Kallioniemi,Kallioniemi,Sudar,Rutovitz,Gray,Waldman,Pinkel-1)，相比更传统的[Giemsa显带](https://zh.wikipedia.org/wiki/G显带)技术和[荧光原位杂交](https://zh.wikipedia.org/wiki/荧光原位杂交)技术（FISH）（受限于所使用的[显微镜](https://zh.wikipedia.org/wiki/显微镜)分辨率），它的分辨率提高到了5-10Mb[[1\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Kallioniemi,Kallioniemi,Sudar,Rutovitz,Gray,Waldman,Pinkel-1)[[2\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Strachan-2)[[3\]](https://zh.wikipedia.org/wiki/比较基因组杂交#cite_note-Weiss,Hermsen,Meijer,VanGrieken,Baak,Kuipers,VanDiest-3)。
  >
  > https://zh.wikipedia.org/wiki/比较基因组杂交

  > ![http://www.cytogen.jp/index/pdf/13-b.pdf](https://s2.ax1x.com/2019/08/12/exej5q.png)

- Spectral karyotyping (SKY)

  > **Spectral karyotyping (SKY)** is a FISH-based method that labels each chromosome with a different color, allowing the identification of the chromosomal origin of all elements of the examined karyotype.
  >
  > From: [Molecular Diagnostics, 2010](https://www.sciencedirect.com/science/article/pii/B9780123694287000215)
  
  > ![Raveendran et al, Malaysian Journal of Medical
  > Sciences. 2013](https://s2.ax1x.com/2019/08/12/exezGV.png)

### 4 SNP genotyping arrays

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

> ![Schematic view of SNP array analysis by Affymetrix (right) and Illumina (left). ](https://s2.ax1x.com/2019/08/01/eaFpLT.png)
>
> Iacobucci I, Lonetti A, Papayannidis C, et al. Use of single nucleotide polymorphism array technology to improve the identification of chromosomal lesions in leukemia[J]. Current cancer drug targets, 2013, 13(7): 791-810.

### 5 CNV Detection

There is a need to develop a high-resolution CNV detection algorithm using high-density SNP genotyping data:

- Identify location of the CNVs
- Estimate the copy numbers
- Model family relationships
- Incorporate *de novo* events

#### 5.1 Log R Ratio (LRR) and B Allele Frequency (BAF)

For both platforms, the computational algorithms convert the **raw signals** into **Log R Ratio (LRR)** and **B Allele Frequency (BAF)**. 

- **LRR** is a measure of normalized total signal intensity. 
- **BAF** is a measure of normalized allelic intensity ratio. 

> BAF = Y / (X + Y)
> LRR = log2( (X + Y)sampleOfInterest / (X+Y)baselineSample)
>
> <https://www.biostars.org/p/199025/>

The combination of LRR and BAF can be used together to determine **different copy numbers** and to differentiate **copy-neutral LOH regions** from normal copy regions. 

> **Loss of heterozygosity (LOH)** is a cross chromosomal event that results in loss of the entire gene and the surrounding chromosomal region.
>
> https://en.wikipedia.org/wiki/Loss_of_heterozygosity

#### 5.2 Detection of CNVs from SNP arrays using PennCNV

![](https://s2.ax1x.com/2019/08/11/ejqBwR.png)

- **Hidden Markov Model (HMM)** is a statistical Markov model in which the system being modeled is assumed to be a Markov process with hidden states.

  > **隐马尔可夫模型**（Hidden Markov Model；[缩写](https://zh.wikipedia.org/wiki/縮寫)：**HMM**）或称作**隐性马尔可夫模型**，是[统计](https://zh.wikipedia.org/wiki/统计)[模型](https://zh.wikipedia.org/wiki/模型)，它用来描述一个含有隐含未知参数的[马尔可夫过程](https://zh.wikipedia.org/wiki/马尔可夫过程)。其难点是从可观察的参数中确定该过程的隐含参数。然后利用这些参数来作进一步的分析，例如[模式识别](https://zh.wikipedia.org/wiki/模式识别)。
  >
  > 在**正常的**马尔可夫模型中，状态对于观察者来说是直接可见的。这样状态的转换概率便是全部的参数。而在**隐**马尔可夫模型中，状态并不是直接可见的，但受状态影响的某些变量则是可见的。每一个状态在可能输出的符号上都有一[概率分布](https://zh.wikipedia.org/wiki/概率分布)。因此输出符号的序列能够透露出状态序列的一些信息。
  >
  > https://zh.wikipedia.org/wiki/隐马尔可夫模型
  >
  > 延伸阅读：[漫谈 Hidden Markov Model](http://freemind.pluskid.org/series/hmm-tutor/)

- What we know are: LRR and BAF

- What we want to know is: **copy number**

##### 5.2.1 PennCNV Flowchart

![](https://s2.ax1x.com/2019/08/11/ejLoDJ.png)

##### 5.2.2 SNP Signal Intensities

![](https://s2.ax1x.com/2019/08/11/evC6Q1.png)

![](https://s2.ax1x.com/2019/08/11/evuH6U.png)

$R=X_{A}+X_{B}$, $θ=(2/π)*arctan(X_{A}/X_{B})$, $LRR=log_2(R_{subject}/R_{expected})$

**X~A~ and X~B**~: normalized signal intensities for alleles A and B

**R~expected**~: calculated based on a reference dataset assuming copy number = 2

> Infinium II is a two-channel assay and data consist of two intensity values (X, Y) for each SNP, with one intensity channel for each of the fluorescent dyes associated with the two alleles of the SNP.
>
> Normalized allele intensities are transformed to a combined SNP intensity, **R** ($R = X + Y$), and an allelic intensity ratio, **theta** ($θ = 2/π*arctan(Y/X)$).
>
> Staaf J, Vallon-Christersson J, Lindgren D, et al. Normalization of Illumina Infinium whole-genome SNP data improves copy number estimates and allelic intensity ratios[J]. BMC bioinformatics, 2008, 9(1): 409.

##### 5.2.3 Visualization of CNVs

![](https://s2.ax1x.com/2019/08/11/evKTUA.png)

##### 5.2.4 Hidden Markov Model in PennCNV

![](https://s2.ax1x.com/2019/08/11/ev3PwF.png)

> Transition probability matrix a~ij~: $a(i,j)= P[q _{t+1}=j|q_{t}=i]$ 
>
> ![](https://s2.ax1x.com/2019/08/11/evyu80.png)
>
> Emission probabilities e~i~(a) probability state i emits character a
>
> ![](https://s2.ax1x.com/2019/08/11/ev6kz6.png)
>
> <http://www.cs.cmu.edu/~durand/03-711/2009/Lectures/hmm09-1.pdf>
>
> <https://www.ncbi.nlm.nih.gov/CBBresearch/Przytycka/download/lectures/PCB_Lect06_HMM.pdf>

- **Emission Probability of LRR**

  Given a copy number state, LRR is normally distributed

  ![](https://s2.ax1x.com/2019/08/11/ev6TOO.png)

- **Emission Probability of BAF**

  ![](https://s2.ax1x.com/2019/08/11/ev6Wk9.png)

##### 5.2.5 Copy Number States

6 states:

- State1: CNV=0 (double deletions)
- State2: CNV=1 (single deletion)
- State3: CNV=2 (normal)
- State4: CNV=2 (normal with LOH)
- State5: CNV=3 (single duplication)
- State6: CNV=4 (double duplications)

每种state在图中有不同的表现

##### 5.2.6 Hidden states, copy numbers, CNV genotypes, and their descriptions

![](https://s2.ax1x.com/2019/08/11/ev3Ih9.png)

### 6 CNV Calling

- Use **Viterbi algorithm** to infer the most likely state path z = 
  (z1, …, zM), by maximizing P(z|r, b, λ).

  > **维特比算法**（英语：**Viterbi algorithm**）是一种[动态规划](https://zh.wikipedia.org/wiki/动态规划)[算法](https://zh.wikipedia.org/wiki/算法)。它用于寻找最有可能产生观测事件序列的**维特比路径**——隐含状态序列，特别是在马尔可夫信息源上下文和隐马尔可夫模型中。
  >
  > https://zh.wikipedia.org/wiki/维特比算法

- Calculation is speed up using **Baum’s forward-backward algorithm**.

  > In [electrical engineering](https://en.wikipedia.org/wiki/Electrical_engineering), [computer science](https://en.wikipedia.org/wiki/Computer_science), [statistical computing](https://en.wikipedia.org/wiki/Statistical_computing) and [bioinformatics](https://en.wikipedia.org/wiki/Bioinformatics), the **Baum–Welch algorithm** is used to find the unknown parameters of a [hidden Markov model](https://en.wikipedia.org/wiki/Hidden_Markov_model) (HMM). It makes use of a [forward-backward algorithm](https://en.wikipedia.org/wiki/Forward-backward_algorithm).
  >
  > https://en.wikipedia.org/wiki/Baum–Welch_algorithm
  >
  > The **forward–backward algorithm** is an [inference](https://en.wikipedia.org/wiki/Statistical_inference) [algorithm](https://en.wikipedia.org/wiki/Algorithm) for [hidden Markov models](https://en.wikipedia.org/wiki/Hidden_Markov_model) which computes the [posterior](https://en.wikipedia.org/wiki/Posterior_probability) [marginals](https://en.wikipedia.org/wiki/Marginal_probability) of all hidden state variables given a sequence of observations/emissions.
  >
  > https://en.wikipedia.org/wiki/Forward–backward_algorithm

- **A CNV is called whenever a stretch of states different from the normal state is observed.** 

- **Algorithm is implemented in software PennCNV.**

#### 6.1 Viterbi algorithm for calling

- Calculate the most likely path in HMM (a path of state 1-6 for each SNP marker)
- Collect any non-normal state path as the CNV calls

#### 6.2 Other Types of Signal Data

PennCNV can be applied to data from other technical platforms:

- Transformation of signal data to LRR/BAF:
  - Affymetrix whole-genome SNP genotyping array
  - Perlegen whole-genome SNP genotyping array
- Use information from LRR only:
  - BAC clone based array-CGH
  - Oligonucleotide arrays
  - Non-polymorphic markers in recent SNP genotyping arrays

#### 6.3 PennCNV-Affy Pipeline

SNP→CNV→genotype call, 但 SNP array 效率较低

![](https://s2.ax1x.com/2019/08/11/evWkrV.png)

#### 6.4 Joint Modeling on Family Data

- Most CNVs demonstrate **Mendelian inheritance**

  ![](https://s2.ax1x.com/2019/08/11/exPCK1.png)

- Incorporate **family relationship** can potentially improve **sensitivity** of CNV calling

- Example of Inherited CNV

  ![](https://s2.ax1x.com/2019/08/11/exiy0P.png)

- Example of de novo CNV
  
  ![](https://s2.ax1x.com/2019/08/11/exi2tS.png)

#### 6.5 Joint modeling of the CNVs in a trio

- A HMM that jointly models a trio simultaneously

  > the `--trio` argument specify that we want to use family-based CNV detection algorithm to jointly update CNV status for a father-mother-offspring trio.
  >
  > <http://penncnv.openbioinformatics.org/en/latest/user-guide/trio/>

- Do not assume that CNV region is already known

![](https://s2.ax1x.com/2019/08/11/exF2Hx.png)

#### 6.6 Likelihood of Signal Intensities

By treating the trio as a unit, this calling algorithm can **avoid** generating calls that are **Mendelian inconsistent** but preserve the ability to allow *de novo* events.

![](https://s2.ax1x.com/2019/08/12/exZvcD.png)

> *Likelihood of an observation sequence given a state sequence, or likelihood of an observation sequencealong a single path* : given an observation sequence $X = {x1, x2, · · · , xT }$ and a state sequence $Q = {q1, · · · , qT }$ (of the same length) determined from a HMM with parameters Θ, the likelihood of X along the path Q is equal to: 
>
> $p(X|Q, Θ) =\prod_{t=1}^T p(x_{i}|q_{i}
> , Θ) = b_{1}(x_{1}) · b_{2}(x_{2})· · · b_{T} (x_{T} )$
>
> <https://www.cs.ubc.ca/~murphyk/Software/HMM/labman2.pdf>

### 7 NGS-based SV detection

SV: Structural Variants

![Escaramís G, et al. Briefings in Functional Genomics, 2015](https://s2.ax1x.com/2019/08/12/exmS2T.png)

特别复杂的SV需要 de novo assemble，contig比对到reference上。

> ( A ) **Read depth** . Reads are aligned into the reference genome and when compared to diploid regions they show a reduced number of reads in a deleted region or higher read depth in a duplicated region.
>
> ( B ) **Paired reads**. Pairs of sequence reads are mapped into the reference genome (from left to right): (1) no SV, pairs are aligned into correct order, correct orientation and spanned as expected based on the library’s insert size; (2) deletion, the aligned pairs span far apart from that expected based on library insert size; (3) tandem duplication, read pairs are aligned in unexpected order, where expected order means that the leftmost read should be aligned in the forward strand and the rightmost read in the reverse strand; (4) novel sequence insertion, the pairs are aligned closer from that expected based on library insert size; (5) inversion, read pairs are aligned in wrong orientation, both reads align either in forward or reverse strand; and (6) read pairs mapped to different chromosomes. 
>
> ( C ) **Split reads**. Sequenced reads pointing to the same breakpoint are split at the nucleotide where the breakpoint occurs. The corresponding paired read is properly aligned to the reference genome. 
>
> ( D ) **De novo assembly**. Sample reads from novel sequence insertions are assembled without a reference sequenced genome. 

> - **Read-Pair (RP)** method is to estimate the **likelihood** of expected value of insert
>   size variation associated with deletion and insertion.
> - **Read-Depth** based algorithm reports **exact number** of sequence copies in the genome.
>
> Ye K, Hall G, Ning Z. Structural variation detection from next generation sequencing[J]. Next Generat Sequenc & Applic, 2016, 1(007).

#### 7.1 Read count-based methods for SV detection

- Detect the change of **read count/sequencing coverage** in a certain region
- Examples of software tools: CNVnator, BIC-SEQ2, PennCNV-Seq
- Limitation:
  - Only detects **unbalanced** events (**copy number variation**)
  - Cannot resolve breakpoints at base pair resolution

#### 7.2 Detection of SVs from discordant read pairs
- Widely used software tools: Delly, Lumpy

- Pattern of **deletions**: large gaps between read pairs

  ![Rausch T, et al. (2012) Bioinformatics](https://s2.ax1x.com/2019/08/12/exmdsg.png)

- Pattern of **inversions**: same orientation between read pairs

  ![Rausch T, et al. (2012) Bioinformatics](https://s2.ax1x.com/2019/08/12/exmron.png)

- Pattern of **tandem duplication**: the first and second read changed their relative order

  ![Rausch T, et al. (2012) Bioinformatics](https://s2.ax1x.com/2019/08/12/exmyiq.png)

- Pattern of **translocations**: paired-ends mapping to different chromosomes

#### 7.5 Detection of SVs using assembly-based methods

> Most short read methods based on assembly for SV detection use a reference assisted approach. Reads with missing pair or unmapped reads after a reference alignment are collected and a local assembly is performed to generate contig that represents the actual local structural variation. 
>
> https://www.1010genome.com/sv-detection/

- **De novo sequence assembly (AS)** enables the fine-scale discovery of SVs, including novel (non-reference) sequence insertions.
- Either **global** or **local** assembly may be used to discover SVs.
- Example tools: 
  - SvABA (genome-wide detection of structural variants and indels by local assembly)
  - novoBreak (local assembly for breakpoint detection in cancer genomes)
  - TIGRA (a targeted iterative graph routing assembler for breakpoint assembly)

#### 7.6 SV detection from long-read sequencing

> Pacbio and Oxford nanopore platforms offer a different view of structural variation in a genome with help of their average >10kb read lengths. A low coverage of 10x for these long reads can help detect a high percentage of structural variations (>80%) in a complex genome. 
>
> https://www.1010genome.com/sv-detection/

- Multiple alignment tools have been developed to **map long reads** to the reference genome

  - Minimap2: a ultra-fast long read alignment tool
  - NGMLR: an aligner that is specifically developed for SV discovery
  - BLASR: a aligner developed for PacBio reads 
  - BWA-MEM: an early aligner for long reads, could be replaced by Minimap2

- Several tools have been developed to **detect SVs** from long read sequencing

  > ![](https://s2.ax1x.com/2019/08/12/ex1snf.png)
  >
  > <http://schatz-lab.org/presentations/2018/2018.PAG.G10kTalk.pdf>

  - SV callers for PacBio reads:
    - PBSV
    - SMRT-SV
    - PBHoney
    -  Sniffles
  - SV callers for Nanopore reads:
    - NanoSV
    - Sniffles
    - Picky

- Short/long reads on SV detection

  ![](https://s2.ax1x.com/2019/08/12/ex1h3n.png)

  绝大多数SV可以通过short read seq找出来

#### 7.7 Bionano optical mapping for SV detection

> Optical mapping technique like Bionano genomics further enhance the ability of NGS based SVs to detect large and complex SVs. Optical mapping generates images of megabase size DNA molecules that in turn produce genome maps.
>
> https://www.1010genome.com/sv-detection/

A **nanopore array** that detects a characteristic 6 or 7-nucleotide sequence along very long genomic segments.

**SV detection from Single-molecule optical mapping** 

- To identify a structural variation, a ***de novo*** genome map assembly can be 
  aligned to a **reference** genome.
- By observing changes in label spacing and comparisons of order, position, and orientation of label patterns, SVs can be detected.

![](https://bionanogenomics.com/wp-content/uploads/2017/07/Technology_supportGraphic_SV_TYPES_revised.jpg)