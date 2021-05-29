> [生信人的linux考试](http://www.bio-info-trainee.com/2900.html)


1. 在任意文件夹下面创建形如 `1/2/3/4/5/6/7/8/9` 格式的文件夹系列。

   ```shell
   $ mkdir -p 1/2/3/4/5/6/7/8/9
   $ tree
   .
   └── 1
       └── 2
           └── 3
               └── 4
                   └── 5
                       └── 6
                           └── 7
                               └── 8
                                   └── 9
   
   9 directories, 0 files
   ```

2. 在创建好的文件夹下面，比如我的是 `/Users/jimmy/tmp/1/2/3/4/5/6/7/8/9` ，里面创建文本文件 `me.txt`

   ```shell
   $ cd 1/2/3/4/5/6/7/8/9
   (base) lyshi 01:46:17 ~/test/1/2/3/4/5/6/7/8/9
   $ touch me.txt
   ```

3. 在文本文件 `me.txt` 里面输入内容:

   ```
   Go to: http://www.biotrainee.com/
   I love bioinfomatics.
   And you ?
   ```

   ```shell
   $ vim me.txt
   $ cat me.txt 
   Go to: http://www.biotrainee.com/
   I love bioinfomatics.
   And you ?
   ```

4. 删除上面创建的文件夹 `1/2/3/4/5/6/7/8/9` 及文本文件 `me.txt`

   ```shell
   $ cd -
   $ rm -r 1
   ```

5. 在任意文件夹下面创建 folder`1~5`这5个文件夹，然后每个文件夹下面继续创建 folder`1~5`这5个文件夹

   ```shell
   $ mkdir -p folder{1..5}/folder{1..5}
   $ tree
   .
   ├── folder1
   │   ├── *
   │   ├── folder1
   │   ├── folder2
   │   ├── folder3
   │   ├── folder4
   │   └── folder5
   ├── folder2
   │   ├── folder1
   │   ├── folder2
   │   ├── folder3
   │   ├── folder4
   │   └── folder5
   ├── folder3
   │   ├── folder1
   │   ├── folder2
   │   ├── folder3
   │   ├── folder4
   │   └── folder5
   ├── folder4
   │   ├── folder1
   │   ├── folder2
   │   ├── folder3
   │   ├── folder4
   │   └── folder5
   └── folder5
       ├── folder1
       ├── folder2
       ├── folder3
       ├── folder4
       └── folder5
   
   31 directories, 0 files
   ```

6. 在第五题创建的每一个文件夹下面都 创建第二题文本文件 `me.txt` ，内容也要一样。(这个题目难度超纲，建议一个月后再回过头来做)

   ```shell
   $ touch folder{1..5}/folder{1..5}/me.txt
   $ tree
   .
   ├── folder1
   │   ├── *
   │   ├── folder1
   │   │   └── me.txt
   │   ├── folder2
   │   │   └── me.txt
   │   ├── folder3
   │   │   └── me.txt
   │   ├── folder4
   │   │   └── me.txt
   │   └── folder5
   │       └── me.txt
   ├── folder2
   │   ├── folder1
   │   │   └── me.txt
   │   ├── folder2
   │   │   └── me.txt
   │   ├── folder3
   │   │   └── me.txt
   │   ├── folder4
   │   │   └── me.txt
   │   └── folder5
   │       └── me.txt
   ├── folder3
   │   ├── folder1
   │   │   └── me.txt
   │   ├── folder2
   │   │   └── me.txt
   │   ├── folder3
   │   │   └── me.txt
   │   ├── folder4
   │   │   └── me.txt
   │   └── folder5
   │       └── me.txt
   ├── folder4
   │   ├── folder1
   │   │   └── me.txt
   │   ├── folder2
   │   │   └── me.txt
   │   ├── folder3
   │   │   └── me.txt
   │   ├── folder4
   │   │   └── me.txt
   │   └── folder5
   │       └── me.txt
   └── folder5
       ├── folder1
       │   └── me.txt
       ├── folder2
       │   └── me.txt
       ├── folder3
       │   └── me.txt
       ├── folder4
       │   └── me.txt
       └── folder5
           └── me.txt
   
   31 directories, 25 files
   ## 每个 me.txt 写同样得内容确实需要过一个月才能会...吧 
   ```

7. 再次删除掉前面几个步骤建立的文件夹及文件

   ```shell
   $ rm -r folder{1..5}
   ```

8. 下载 `http://www.biotrainee.com/jmzeng/igv/test.bed` 文件，后在里面选择含有 `H3K4me3` 的那一行是第几行，该文件总共有几行。

   ```shell
   $ wget http://www.biotrainee.com/jmzeng/igv/test.bed
   $ grep -n 'H3K4me3' test.bed 
   8:chr1	9810	10438	ID=SRX387603;Name=H3K4me3%20(@%20HMLE);Title=GSM1280527:%20HMLE%20Twist3D%20H3K4me3%20rep2%3B%20Homo%20sapiens%3B%20ChIP-Seq;Cell%20group=Breast;<br>source_name=HMLE_Twist3D_H3K4me3;cell%20type=human%20mammary%20epithelial%20cells;transfected%20with=Twist1;culture%20type=sphere;chip%20antibody=H3K4me3;chip%20antibody%20vendor=Millipore;	222	.	9810	10438	0,226,255
   $ less test.bed |wc -l
   10
   ```

   > 有 `H3K4me3` 的那一行是第几行：8
   >
   > 该文件总共有几行：10

9. 下载 `http://www.biotrainee.com/jmzeng/rmDuplicate.zip` 文件，并且解压，查看里面的文件夹结构

   ```shell
   $ wget http://www.biotrainee.com/jmzeng/rmDuplicate.zip
   $ unzip rmDuplicate.zip
   Archive:  rmDuplicate.zip
      creating: rmDuplicate/
      creating: rmDuplicate/picard/
      creating: rmDuplicate/picard/paired/
     inflating: rmDuplicate/picard/paired/readme.txt  
     inflating: rmDuplicate/picard/paired/tmp.header  
     inflating: rmDuplicate/picard/paired/tmp.MarkDuplicates.log  
     inflating: rmDuplicate/picard/paired/tmp.metrics  
     inflating: rmDuplicate/picard/paired/tmp.rmdup.bai  
     inflating: rmDuplicate/picard/paired/tmp.rmdup.bam  
     inflating: rmDuplicate/picard/paired/tmp.sam  
     inflating: rmDuplicate/picard/paired/tmp.sorted.bam  
      creating: rmDuplicate/picard/single/
     inflating: rmDuplicate/picard/single/.MarkDuplicates.log  
     inflating: rmDuplicate/picard/single/readme.txt  
     inflating: rmDuplicate/picard/single/tmp.header  
     inflating: rmDuplicate/picard/single/tmp.MarkDuplicates.log  
     inflating: rmDuplicate/picard/single/tmp.metrics  
     inflating: rmDuplicate/picard/single/tmp.rmdup.bai  
     inflating: rmDuplicate/picard/single/tmp.rmdup.bam  
     inflating: rmDuplicate/picard/single/tmp.sam  
     inflating: rmDuplicate/picard/single/tmp.sorted.bam  
      creating: rmDuplicate/samtools/
      creating: rmDuplicate/samtools/paired/
     inflating: rmDuplicate/samtools/paired/readme.txt  
     inflating: rmDuplicate/samtools/paired/tmp.header  
     inflating: rmDuplicate/samtools/paired/tmp.rmdup.bam  
     inflating: rmDuplicate/samtools/paired/tmp.rmdup.vcf.gz  
     inflating: rmDuplicate/samtools/paired/tmp.sam  
     inflating: rmDuplicate/samtools/paired/tmp.sorted.bam  
     inflating: rmDuplicate/samtools/paired/tmp.sorted.vcf.gz  
      creating: rmDuplicate/samtools/single/
     inflating: rmDuplicate/samtools/single/readme.txt  
     inflating: rmDuplicate/samtools/single/tmp.header  
     inflating: rmDuplicate/samtools/single/tmp.rmdup.bam  
     inflating: rmDuplicate/samtools/single/tmp.rmdup.vcf.gz  
     inflating: rmDuplicate/samtools/single/tmp.sam  
     inflating: rmDuplicate/samtools/single/tmp.sorted.bam  
     inflating: rmDuplicate/samtools/single/tmp.sorted.vcf.gz
   $ tree rmDuplicate
   rmDuplicate
   ├── picard
   │   ├── paired
   │   │   ├── readme.txt
   │   │   ├── tmp.header
   │   │   ├── tmp.MarkDuplicates.log
   │   │   ├── tmp.metrics
   │   │   ├── tmp.rmdup.bai
   │   │   ├── tmp.rmdup.bam
   │   │   ├── tmp.sam
   │   │   └── tmp.sorted.bam
   │   └── single
   │       ├── readme.txt
   │       ├── tmp.header
   │       ├── tmp.MarkDuplicates.log
   │       ├── tmp.metrics
   │       ├── tmp.rmdup.bai
   │       ├── tmp.rmdup.bam
   │       ├── tmp.sam
   │       └── tmp.sorted.bam
   └── samtools
       ├── paired
       │   ├── readme.txt
       │   ├── tmp.header
       │   ├── tmp.rmdup.bam
       │   ├── tmp.rmdup.vcf.gz
       │   ├── tmp.sam
       │   ├── tmp.sorted.bam
       │   └── tmp.sorted.vcf.gz
       └── single
           ├── readme.txt
           ├── tmp.header
           ├── tmp.rmdup.bam
           ├── tmp.rmdup.vcf.gz
           ├── tmp.sam
           ├── tmp.sorted.bam
           └── tmp.sorted.vcf.gz
   
   6 directories, 30 files
   ```

10. 打开第九题解压的文件，进入 `rmDuplicate/samtools/single` 文件夹里面，查看后缀为 `.sam` 的文件，搞清楚 生物信息学里面的`SAM/BAM` 定义是什么。

    > - SAM: 
    >
    >   - Sequence Alignment/Map format 
    >
    > - BAM: 
    >
    >   - Binary representation of SAM 
    >   -  Block compression in BGZF format; each BGZF block itself a spec-compliant gzip archive 
    >   - Facilitating random accessing to the BAM file for indexed queries
    >
    > <https://sites.google.com/site/bioinformaticsremarks/bioinfo/sam-bam-format>

    > Lets face it there aren’t too many HTS pipelines that don’t touch a SAM/BAM files somewhere along the way. The name SAM comes from the acronym [Sequence Alignment/Map](http://samtools.github.io/hts-specs/SAMv1.pdf) format and they are really nothing more than tab delineated text files describing mapping information for short read data. Of course with the millions of reads generated by next gen sequencers these days the files become really really big. That is where BAM files come in, these contain the same information as SAM files but are encoded in condensed computer readable binary format to save disk space.
    >
    > If you open up a SAM file in a text editor (or use “head” in bash) the first part of the file typically contains lines beginning with AT (@) symbol, which represents the “header”. The header contains general information about the alignment, such as the reference name, reference length, MD5 checksum, the program used for the alignment, etc etc. Every line after the header represents a single read, with 11 mandatory tab separated fields of information. Lets see how this works with a really simple example.
    >
    > <https://dwheelerau.com/2015/02/28/sam-bam-files-explained/>

    > <https://samtools.github.io/hts-specs/SAMv1.pdf>

    > <https://en.wikipedia.org/wiki/SAM_(file_format)>

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)

    

