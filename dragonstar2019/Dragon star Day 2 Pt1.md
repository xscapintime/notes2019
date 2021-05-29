# Dragon star Day 2 Pt.1

> [Dragonstar2019 by Kai Wang](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FWGLab%2Fdragonstar2019)
>
> 1. Alignment of short/long-read sequencing data
> 2. Genome assembly by short/long-read sequencing

[TOC]



## Part Ⅰ Alignment of short/long-read sequencing data

### 1 Sequence similarity

>Sequence similarity is a measure of an empirical relationship between sequences.  
>
>A similarity score is therefore aimed to approximate the evolutionary distance between a pair of nucleotide or protein sequences.
>
>Heringa, Jaap(Jul 2008) Sequence Similarity. In: eLS. John Wiley & Sons Ltd, Chichester. http://www.els.net [doi: 10.1002/9780470015902.a0005317.pub2]

### 2 Sequence alignment

- Consider matches, mismatches, insertions and deletions 
  (indels are gaps)
- Find an **optimal** alignment between sequences
- Applications
  - Reference-based read mapping
  - Genome assembly
  - Gene finding

#### 2.1 Pairwise alignment

Sequence alignment between **two** sequences 

- Find an **optimal** alignment between two sequences

### 3 Dynamic programming

> **动态规划**（英语：Dynamic programming，简称DP）是一种在[数学](https://zh.wikipedia.org/wiki/数学)、[管理科学](https://zh.wikipedia.org/wiki/管理科学)、[计算机科学](https://zh.wikipedia.org/wiki/计算机科学)、[经济学](https://zh.wikipedia.org/wiki/经济学)和[生物信息学](https://zh.wikipedia.org/wiki/生物信息学)中使用的，通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。
>
> 动态规划常常适用于有重叠子问题和[最优子结构](https://zh.wikipedia.org/w/index.php?title=最优子结构&action=edit&redlink=1)性质的问题，动态规划方法所耗时间往往远少于朴素解法。
>
> 动态规划背后的基本思想非常简单。大致上，若要解一个给定问题，我们需要解其不同部分（即子问题），再根据子问题的解以得出原问题的解。
>
> *https://zh.wikipedia.org/wiki/动态规划*

- Purpose
  - To find an alignment between two sequences with best matching scores
- Three components
  - Recursive calculation - 递归计算
  - Tabular arrangement - 填充得分矩阵
  - Traceback - 追溯本源
- Three common types of pairwise alignments
    - Global alignment: Needleman-Wunsch
    - Local alignment: Smith-Waterman
    - Semi-global alignment

#### 3.1 Global alignment: Needleman-Wunsch

- **Best** global alignment

  Have **maximal alignment score** with all bases in two sequences

- Score function

  ![](https://s2.ax1x.com/2019/08/09/eb7Q1I.png)

##### 3.1.1 Working of Needleman -Wunsch Algorithm

> The dynamic programming matrix is defined with three different steps. 
>
> 1. Initialization of the matrix with the scores possible.
> 2. Matrix filling with maximum scores.
> 3. Trace back the residues for appropriate alignment.
>
> *vlab.amrita.edu,. (2012). Global alignment of two sequences - Needleman-Wunsch Algorithm. Retrieved 9 August 2019, from vlab.amrita.edu/?sub=3&brch=274&sim=1431&cnt=1* 

- Assume we have two sequences: P and Q

  - P = TCATGGC
  - Q = TCATC

- **Initial matrix**

  ![](https://s2.ax1x.com/2019/08/09/eqJoVJ.png)

- **Initialization Step**

  > Gap score is defined as penalty given to alignment, when we have insertion or deletion.
  >
  > This example assumes that there is gap penalty.  
  >
  > 从 Score function 可知空位罚分为1
  >
  > *vlab.amrita.edu,. (2012). Global alignment of two sequences - Needleman-Wunsch Algorithm. Retrieved 9 August 2019, from vlab.amrita.edu/?sub=3&brch=274&sim=1431&cnt=1* 

  ![](https://s2.ax1x.com/2019/08/09/eqGxNn.png)

- **Matrix Fill Step**

  ![](https://s2.ax1x.com/2019/08/09/eqSulD.png)
  
  全部写出来太乱了，就先杰样吧
  
  ![](https://s2.ax1x.com/2019/08/09/eqJu36.png)

- **Trace back Step**

  > The final step in the algorithm is the trace back for the best alignment.
  >
  > By continuing the trace back step by the above defined method, one would reach to the 0th row, 0th column. 
  >
  >  The best alignment among the alignments can be identified by using the maximum alignment score which may be user defined.
  >
  > *vlab.amrita.edu,. (2012). Global alignment of two sequences - Needleman-Wunsch Algorithm. Retrieved 9 August 2019, from vlab.amrita.edu/?sub=3&brch=274&sim=1431&cnt=1* 

  ![](https://s2.ax1x.com/2019/08/09/eqi8Rx.png)

  ![](https://s2.ax1x.com/2019/08/09/eqiQo9.png)

  多种比对结果：

  ```
  purple line:
  TCATGGC
  ||||--|
  TCAT--C
  
  blue line:
  TCATGGC
  ||||-|-
  TCAT-C-
  
  yellow line:
  TCATGGC
  |||||--
  TCATC--
  ```


#### 3.2 Local alignment: Smith-Waterman

> **史密斯-沃特曼算法**（Smith-Waterman algorithm）是一种进行局部序列比对（相对于全局比对）的算法，用于找出两个核苷酸序列或蛋白质序列之间的相似区域。该算法的目的不是进行全序列的比对，而是找出两个序列中具有高相似度的片段。
>
> 该算法由[坦普尔·史密斯](https://zh.wikipedia.org/wiki/坦普尔·史密斯)（Temple F. Smith）和[迈克尔·沃特曼](https://zh.wikipedia.org/wiki/迈克尔·沃特曼)（Michael S. Waterman）于1981年提出。史密斯-沃特曼算法是[尼德曼-翁施算法](https://zh.wikipedia.org/wiki/尼德曼-翁施算法)的一个变体，二者都是动态规划算法。这一算法的优势在于可以在给定的打分方法下找出两个序列的最优的局部比对（打分方法使用了置换矩阵和空位罚分）。该算法和尼德曼-翁施算法的主要区别在于该算法不存在负分（负分被替换为零），因此局部比对成为可能。回溯从分数最高的矩阵元素开始，直到遇到分数为零的元素停止。分数最高的局部比对结果在此过程中产生。
>
> ![](https://upload.wikimedia.org/wikipedia/commons/a/a8/Smith-Waterman-Algorithm-Example.gif)
>
> https://zh.wikipedia.org/史密斯-沃特曼算法

- **Best** local alignment

  Have **maximal alignment score** with a subset of bases in two sequences

- Score function

  ![](https://s2.ax1x.com/2019/08/09/eb7Q1I.png)

##### 3.2.1 Working of Smith-Waterman Algorithm

> The basic steps for the algorithm are similar to that of Needleman-Wunsch algorithm. The steps are:
>
> 1.	Initialization of a matrix.
> 2.	Matrix Filling with the appropriate scores.
> 3.	Trace back the sequences for a suitable alignment.
>
> *vlab.amrita.edu,. (2012). Smith-Waterman Algorithm - Local Alignment of Sequences. Retrieved 9 August 2019, from vlab.amrita.edu/?sub=3&brch=274&sim=1433&cnt=1*

可见步骤都是一致的，用同样的序列和 score function 进行比对
- Assume we have two sequences: P and Q

  - P = TCATGGC
  - Q = TCATC
  
- **Initial matrix**

  ![](https://s2.ax1x.com/2019/08/09/eqJoVJ.png)

- **Initialization Step**

  ![](https://s2.ax1x.com/2019/08/09/eqcxLd.jpg)
  
- **Matrix Fill Step**

  ![](https://s2.ax1x.com/2019/08/09/eqg8lF.png)

- **Trace back the sequences for a suitable alignment**

  1. Find a best score recursively
  2. Check which operation is used to obtain the current alignment score
  3. Stop when an alignment is 0

  所以最后应该是这个亚子：

  ![](https://s2.ax1x.com/2019/08/09/eq2KnH.png)

  ```
  TCAT
  ||||
  TCAT
  ```


### 4 BLAST: The seed-index-map-extend-merge strategy

> The BLAST = **B**asic **L**ocal **A**lignment **S**earch **T**ool algorithm is a heuristic for computing optimal "local alignments" between a query sequence and a database containing one or more subject sequences.
>
> ![](https://s2.ax1x.com/2019/08/09/eqWWkR.png)
>
> <https://ab.inf.uni-tuebingen.de/teaching/ws2012/bioinf1/03-BLAST-BLAT.pdf>

#### 4.1 BLAST Basics

- BLAST is used for sequence **similarity** search, and much faster than Smith-Waterman method.
- Compare a query sequence against a **database** of sequences. 
- BLAST is a **collection** of algorithms
  - BLAST**N**: **nucleotide** sequence against **nucleotide** database
  - BLAST**P**: **protein** sequence against **protein** database
  - BLAST**X**: **translated** **nucleotide** sequence against **protein** database
  - **T**BLAST**X**: **six-frame translations of a nucleotide** query sequence 
    against the **six-frame translations of a nucleotide sequence** database 
  - **T**BLAST**N**: **protein** sequence against **translated nucleotide** database

#### 4.2 Process: The seed-index-map-extend-merge strategy

> Most popular algorithms use a seed-and-extend approach that operates in two steps:
> 1. Find a set of short exact matches called seeds.
> 2. Try to extend each seed match to obtain a long inexact match
>
> These matching words are called **seeds** and BLAST then attempts to extend each seed to a **HSP** (High-Scoring Segment Pair).
>
> https://ab.inf.uni-tuebingen.de/teaching/ws2012/bioinf1/03-BLAST-BLAT.pdf>

##### 4.2.1 The Process

- Seed and index

  - Construct common words (k-mers) for sequences in a database

  - Assume the database has two sequences and k=3

    > A “k-mer” is a word of DNA that is k long.
    >
    > Typically we extract k-mers from genomic assemblies or read data sets by running a k-length window across all of the reads and sequences.
    >
    > k-mers are most useful when they’re *long*, because then they’re *specific*. 
    >
    > The important concept here is that **long k-mers are species specific**. 
    >
    > <https://angus.readthedocs.io/en/2017/kmers-and-sourmash.html>

- Assume all 3-mers are high-score words

  BLAST needs a threshold to determine high-score words.

- Seed-map with high-score words

  Obtain words from a query sequence

- Extend-merge

  - Extend: high-scoring segment

  - BLAST output: 2 alignment containing match information and **E-value** for each alignment

    E-value 的解释见 4.2.2

##### 4.2.2 Statistical significance of alignment

- How to assess the significance of a high-scoring hit to the database?

  > One crucial feature of the BLAST algorithm is that it gives an estimation of the statistical signficance of a determined **HSP**. This is based on so-called **Karlin-Altschul statistics** for local alignments (without gaps), which involves the **Poisson** distribution and other concepts.
  >
  > 即存在 E-value, P-value
  >
  > https://ab.inf.uni-tuebingen.de/teaching/ws2012/bioinf1/03-BLAST-BLAT.pdf>

  **E** is the number of alignments expected by chance during a database search.

- Karlin-Altschul equation: $E(S) = Kmne^{-λS}$

  - Score (S) from scoring matrix
  - 𝐾 and λ are two constants
  - 𝑚: the length of a query sequence 
  - 𝑛: the sum of bases of all sequences in database
  - The lower E value indicates more significant alignment.

  > E value represents the **number of matches with score ≥ S** that one would expect to see between two random sequences of lengths *n* and *m*.
  >
  > https://ab.inf.uni-tuebingen.de/teaching/ws2012/bioinf1/03-BLAST-BLAT.pdf>
  
- How to assess the P-value of finding a high-scoring pair (HSP)?

  - The number of random HSPs with score >= S is described by a **Poisson** distribution 

  - The **probability** of finding **exactly k HSPs** with score >=S is given by $P(X = k) = e^{−E} *E^k/k!$

  - The **probability** of finding **at least one HSP** by chance is 

    $P(S) = 1 − P(X = 0) = 1 − e^{−E}$

- E-value vs P-value
  - $E = − ln(1 − P)$
  - When E and P are very **small**, they are almost **identical**

### 5  BWT 

> Technically, it is a **lexicographical** reversible permutation of the characters of a string. 
>
> The most important application of BWT is found in biological sciences where genomes(long strings written in A, C, T, G alphabets) don’t have many runs but they do have many repeats.
>
> <https://www.geeksforgeeks.org/burrows-wheeler-data-transform-algorithm/>

#### 5.1 Bowtie/BWA

> **Bowtie** is an ultrafast, memory-efficient short read aligner. It aligns short DNA sequences (reads) to the human genome at a rate of over 25 million 35-bp reads per hour. Bowtie indexes the genome with a Burrows-Wheeler index to keep its memory footprint small: typically about 2.2 GB for the human genome (2.9 GB for paired-end).
>
> <http://bowtie-bio.sourceforge.net/index.shtml>

> **BWA** is a software package for mapping low-divergent sequences against a large reference genome, such as the human genome. It consists of three algorithms: BWA-backtrack, BWA-SW and BWA-MEM. 
>
> <http://bio-bwa.sourceforge.net/>

BWA and Bowtie are the most famous **implementations of BWT** in sequence alignment.

#### 5.2 BWT procedures

![](https://s2.ax1x.com/2019/08/10/eqxZdI.png)

- Given a string S = “TCATC”, BWT
  - Adds `$` and assume `$`<any alphabet 在最后加上$
  - Obtains a suffix array of all cyclic rotations 轮转后缀矩阵

- Cyclic rotation
  - Keeps an index of the rotated strings in the array 前面的数字
  - Creates Circular Permutation Table (CPT) 表格本身
- Sorting
  - Sorts the Circular Permutation Table (CPT) **alphabetically** 
  - Keep the index with the strings 数字不变
  - Taking the **last column** from the sorted array 
- BWT output
  - The last column represents the transformed string

#### 5.3 BWT reverse transformation

With a suffix array, it is easy to recover the **original string** S

![](https://s2.ax1x.com/2019/08/10/eqz97n.png)

#### 5.4 BWT search












