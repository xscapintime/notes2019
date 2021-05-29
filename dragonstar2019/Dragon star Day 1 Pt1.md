# Dragon star Day 1

> [Dragonstar2019 by Kai Wang](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FWGLab%2Fdragonstar2019)
>
> 1. **Genomic technologies in disease studies**
> 2. NGS data formats and variant calling
[TOC]

## Part Ⅰ Genomic technologies in disease studies

### 1 Types of genetic variation

- **SNVs** (Single Nucleotide Variants) - 单核苷酸变异

- **Indel** (Insertion or deletion < 50 bp)

- **SV** (Structural Variants)  - 结构变异

  can be *balanced* or *unbalanced*
  
  - **Balanced** events do not involve gain or loss of genetic materials
  - Inversions and translocations
    - Complex SVs (several types together)
  
  - **Unbalanced** events:
  - deletions/insertions/duplications (Deletions and duplications are two subtypes of **CNVs** (Copy Number Variants)
    - Chromosomal aneuploidies (such as *trisomy 21* 21三体) are *extreme* cases of unbalanced SV.
  
- Allele frequency and effect size - 等位基因频率与效应量/效应值

    ![Manolio et al, Nature, 2009](https://s2.ax1x.com/2019/07/29/e8gcXq.png)
  
    > Genome-wide association studies (GWAS) are effective in detecting **common alleles** that contribute to the inherited component of **common multifactorial diseases**. Typically, the alleles identified by this approach have **modest effect sizes** that cannot fully account for disease susceptibility. This discrepancy may exist because it is hard to identify rare alleles with a low to modest penetrance using GWAS. Penetrance is a measure of the proportion of individuals in a population carrying a particular allele that expresses the related phenotype. In contrast to multifactorial diseases, Mendelian diseases have a high penetrance and very rare allele frequency.
    >
    > McCarthy, Mark I., et al. "Genome-wide association studies for complex traits: consensus, uncertainty and challenges." Nature reviews genetics 9.5 (2008): 356.
    
    > 在[统计学](https://zh.wikipedia.org/wiki/統計學)中，**效应值**（Effect size）是量化现象强度的数值。[[1\]](https://zh.wikipedia.org/wiki/效应值#cite_note-Kelley2012-1)效应值实际的统计量包括了二个变数间的[相关程度](https://zh.wikipedia.org/wiki/相關)、回归模型中的[回归系数](https://zh.wikipedia.org/wiki/線性回歸)、不同处理间[平均值](https://zh.wikipedia.org/wiki/平均值)的差异……等等。无论哪种效应值，其[绝对值](https://zh.wikipedia.org/wiki/絕對值)越大表示效应越强，也就是现象越明显。效应值与[特效检验](https://zh.wikipedia.org/wiki/假設檢定)的概念是互补的。在估算[统计检定力](https://zh.wikipedia.org/wiki/統計檢定力)、需要的[样本数](https://zh.wikipedia.org/w/index.php?title=樣本數&action=edit&redlink=1)与进行[元分析](https://zh.wikipedia.org/wiki/元分析)时，效应值经常扮演重要角色。
    >
    > https://zh.wikipedia.org/wiki/效应值
    
    > Effect size is a simple way of quantifying the difference between two groups that has many advantages over the use of tests of statistical significance alone. Effect size emphasises the size of the difference rather than confounding this with sample size.* 
    >
    > Coe R. It's the effect size, stupid: What effect size is and why it is important[J]. 2002.

### 2 Revolution: single-molecule long-read sequencing 

单分子长读长测序

#### 2.1 Oxford Nanopore Sequencing 

测量电流

The DNA/RNA is sequenced when it is going though a protein pore. 

- DNA (or RNA)
- Motor protein
- Adapter sequence

The nucleotides in the DNA/RNA block the **ionic current** and induce changes of current, which can be measured. 

#### 2.2 PacBio Single-molecule real-time (SMRT) sequencing

测量荧光

- SMRTbell library: 环状

- Adapter: 环状

- Types of SMRT sequencing reads

  - Continuous Long Reads (CLR)

    > Long inserts so that the polymerase can synthesize along a single strand

  - Circular Consensus Sequencing (CCS， HiFi）

    > Short inserts, so polymerase can continue around the entire SMRTbell multiple times and generate multiple sub-reads from the same single molecule

  - 存在由于技术造成的 error insertion
  
    Insertions tend to be more than deletions and substitutions.

#### 2.3 Linked-read Sequencing

依靠barcode

By adding a unique *barcode* to every short read generated from an individual molecule, the **short reads are linked together.**

> 10X Genomics 公司的 linked reads 技術本質上是將 barcode 序列引入長序列片段，透過將長片段分配到不同的油滴微粒中，利用 GemCode 平台對長片段序列進行擴增引入 barcode 序列以及定序接頭引物，然後將序列打斷成適合定序大小的片段進行定序，通過 barcode 序列資訊追踪來自每個大片段DNA模板的多個 Reads，從而獲得大片段的遺傳資訊。通過 linked reads 結合常規二代定序組裝得到的Scaffold，可建構準確度更長的Scaffold。
>
> http://toolsbiotech.blog.fc2.com/blog-entry-37.html

> **What are molecular barcodes?**
> The concept of molecular barcodes is that each original DNA fragment, within the same sample, is attached to a unique sequence barcode. 
>
> <https://pdfs.semanticscholar.org/310b/3bac42989485c98406848217418ff22c22e7.pdf>

- Linked read use molecular barcoding to preserve long-range information

- Short read pairs (2 x150 bp) are generated using barcode-containing primers.
- Short reads that contains the **same barcode** and within a certain distance can be **linked together** to “reconstruct” the original long DNA fragment.

#### 2.4 Single-molecule optical mapping (Bionano Genomics) 

依靠限制性内切酶位点，测量光谱

> **Optical mapping** 
>
> By mapping the location of **restriction enzyme sites** along the unknown DNA of an organism, the spectrum of resulting DNA fragments collectively serves as a unique "fingerprint" or "barcode" for that sequence. 
>
> <https://en.wikipedia.org/wiki/Optical_mapping>