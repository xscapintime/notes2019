# Dragon star Day 3

> [Dragonstar2019 by Kai Wang](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FWGLab%2Fdragonstar2019)
>
> 1. Detection of structural variants in human 
> 2. Annotation and phenotype-driven interpretation of genetic variants

[TOC]

## Part Ⅱ Annotation and phenotype-driven interpretation of genetic variants

### 1 ANNOVAR for variant annotation

![](https://s2.ax1x.com/2019/08/13/mpIw5t.png)

ANNOVAR有shell命令行及网页工具两种使用方式：

![](https://s2.ax1x.com/2019/08/13/mpIBPP.png)

#### 1.1 Similar tools

- Command line programs include:
  - VEP
  - snpEf
  - VAAST
  - AnnTools
  - Jannovar
- Web servers include:
  - VAT
  - SeattleSeq
  - AVIA
  - VARIANT

#### 1.2 ANNOVAR supports 3 types of annotations:

##### 1.2.1 Gene-based annotation (g)

Several commonly used **gene definitions**:

- RefSeq gene - NCBI
- UCSC known gene - UCSC
- ENSEMBL gene - Europe
- GENCODE gene - a project, all 

**precedence rules**

##### 1.2.2 Region-based annotation (r)

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

##### 1.2.3 Filter-based annotation (f)

Several commonly used filter annotation:

- Allele frequency in various databases
- Presence in various association databases
- Functional prediction scores
- dbSNP identifier

#### From functional annotation to clinical interpretation
##### 5-tier system for clinical interpretation

##### InterVar: Automated implementation of the ACMG-AMP clinical interpretation guidelines
### Introduction to HPO

The Human Phenotype Ontology (HPO) 

Our goal: from clinical phenotypes to genes

#### Phenolyzer