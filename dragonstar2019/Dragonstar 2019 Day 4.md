# Dragonstar 2019 Day 4

## SNP and Sequencing-based genome-wide association studies (GWAS)
### Important Questions in Human Genetics
### Genome-wide Association Studies (GWAS)

#### Common Disease Common Variant Hypothesis

- Capturing Common Variation

##### The International HapMap Project

##### The 1000 Genomes Project

- Concepts Underlying the GWAS Design

- Linkage Disequilibrium (LD)

  Describes the degree to which an allele of one SNP is correlated with an allele of another SNP within a population.

### Quality Control in GWAS
- Sample level quality control

- SNP level quality control

- Check of Call Rate and Heterozygosity

- Check of Gender Concordance

- Check of Sample Relatedness

- Check of Mendelian Inheritance Errors

- Check Hardy-Weinberg Equilibrium (HWE)

  > The *Hardy*–*Weinberg principle*, also known as the *Hardy*–*Weinberg equilibrium*, model, theorem, or law, states that allele and genotype frequencies in a population will remain constant from generation to generation in the absence of other evolutionary influences.
  >
  > [https://en.wikipedia.org/wiki/Hardy%E2%80%93Weinberg_principle](https://en.wikipedia.org/wiki/Hardy–Weinberg_principle)

- Check of Batch Effects

  illumina误差大，affy误差小？

#### Importance of QC

### Association Test in GWAS

#### Graphical Display of Results

- Manhattan plot

- QQ plot

  - Remarks on QQ Plot

    不能直接用p.val<0.05判断

  - Multiple Testing Correction

    Perform 1,000,000 tests in a typical GWAS

  - Replication

### From SNP based to sequencing based GWAS
#### High coverage whole genome sequencing

#### Low coverage whole genome sequencing

missing data in the remaining sites → borrowing information from other individuals

- Recipe for Imputation with NGS Data

- Hidden Markov Model for Genotype Imputation
- Comparison Summary: Variant Detection

#### The SardiNIA Project

---

## Rare variants and de novo variants association studies
### Projects

- NHLBI exome sequencing project (ESP)

- Exome Aggregation Consortium (ExAC)

- gnomAD is a public database of rare variants from genome/exome

  ExAC的升级版

### Three Levels of Rare Variant Data

- Level 1: Individual-level

- Level 2: Summarized by Subjects

- Level 3: Summarized by subjects and gene

  HDL group?


### Burden Tests 负担/负荷检测

- Cohort Allelic Sums Test (**CAST**)
- Combined Multivariate and Collapsing (**CMC**) Method

Burden Tests: Mixed Effect: Burden tests will *lose power* if variants have positive and negative effects.

Adaptive Burden Tests: 将direction加入检测

- Adaptive Sum Test 太慢

- Variance-Component Tests 比较快

  - C-alpha Test: 

    Advantage: *robust* in the presence of opposite association directions.

    Disadvantage: cannot adjust for covariates.

  - Sequence Kernel Sequential Test (SKAT)

  - Combine P-values of Burden & SKAT

    考虑到了更多的可能性

  - Unified Test Statistic — SKAT-O

    Combined test of Burden tests and SKAT

### Maximizing the Power

#### Strategies to find high impact novel risk genes

### Developmental disorders

de novo mutations 概率0.8？

#### Strategies of finding novel risk genes from de novo mutations
- Statistical evidence alone

  poisson test

- Data-driven FDR
- Network enrichment
- Rank by functional similarity to known risk genes

#### Putting de novo mutations and inherited mutations together
- TADA-Annotations (TADA-A)

  de novo mutaton + genetic mutation **+ functional annotations**







