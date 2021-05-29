#### 笔记从进阶命令开始

- cut

  ```shell
  zless -S /teach/rna_testdata/database/gtf/gencode.v29.annotation.gtf.gz |cut -f 1,3-5 |less -S
  ```

- paste

  ```shell
  seq 10 |paste - - - -
  ```
  
  ```shell
  seq 10 |paste - - - -|cut -f 1,2
  ```
  
  ![image](https://upload-images.jianshu.io/upload_images/14383117-a72261a89ee13330.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- 利用多种命令实现 fastq 转 fasta

  > fastq 每4行一个单元

  ```shell
  zless -S SRR1039510_1.fastq.gz|less -S
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-754e74024bedaf47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```shell
  zless -S SRR1039510_1.fastq.gz|paste - - - -|less -S
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-59860c29defc7ae9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```shell
  zless -S SRR1039510_1.fastq.gz|paste - - - -|cut -f 1,2|tr '\t' '\n'|less -S
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-755efec76c8f80ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```shell
  zless -S SRR1039510_1.fastq.gz|paste - - - -|cut -f 1,2|tr '\t' '\n'|tr '@' '>'|less -S
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-8d500b69617b2387.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- paste 转换布局

  ```shell
  ls *.gz|paste - -|less -S
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-cddc2e2da48af6e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  ```shell
  ls /teach/rna_testdata/project/1.rna/2.raw_fq/*.gz|paste - -|less -S|cut -f 1 > 1
  ls /teach/rna_testdata/project/1.rna/2.raw_fq/*.gz|paste - -|less -S|cut -f 2 > 2
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-6ec2575946d146ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  从而能够将文件分类，以便后续处理
  
  ![image](https://upload-images.jianshu.io/upload_images/14383117-591c4be701182c0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
- sort

  ```shell
  zcat gencode.v29.annotation.gtf.gz|cut -f 1|less -S
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-6631c1e7c194ff8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  sort -u 去重

  ```shell
  zcat gencode.v29.annotation.gtf.gz|cut -f 1|sort -u
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-21d808eb1581c928.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- uniq

  uniq -c 去重并显示出现次数

  ```shell
  zcat gencode.v29.annotation.gtf.gz|cut -f 1|sort|uniq -c
  ```

  ![image](https://upload-images.jianshu.io/upload_images/14383117-3a6a70c5e7de0c50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- find

  在指定路径下搜索文件
  
  ```shell
  $ find ./ -name "*gz"
  ./gencode.v25.annotation.gtf.gz
  ./gencode.v29.annotation.gtf.gz
  ```
  
- tr

  ```shell
  $ echo $PATH
  /home/lyshi/miniconda3/bin:/home/lyshi/miniconda3/condabin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
  ```

  将 ":" 替换为换行符 "\n"

  ![image](https://upload-images.jianshu.io/upload_images/14383117-0d8622eb945a296a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- wc

  默认输出：行数、字数、字节数

  ```shell
  $ cat ~/.bashrc |wc
      134     575    4367
  ```

  分别 wc -l, wc -w, wc -m

  ![image](https://upload-images.jianshu.io/upload_images/14383117-341eeb6ad99b8289.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- bc 进入数学运算

  ![image](https://upload-images.jianshu.io/upload_images/14383117-bbddcb828438c185.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  scale= 设置小数点位数

  ![image](https://upload-images.jianshu.io/upload_images/14383117-699e7299c9898f5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- sed

  用 sed 完成替换

  将 ":" 替换为 "####"

  ```shell
  $ echo $PATH
  /home/lyshi/miniconda3/bin:/home/lyshi/miniconda3/condabin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
  $ echo $PATH|sed 's/:/####/g'
  /home/lyshi/miniconda3/bin####/home/lyshi/miniconda3/condabin####/usr/local/sbin####/usr/local/bin####/usr/sbin####/usr/bin####/sbin####/bin####/usr/games####/usr/local/games####/snap/bin
  ```

- grep 查找文件中符合条件的行
- awk 对特定的行中特定列进行操作

#### 习题

1. 统计reads_1.fq 文件中共有多少条序列信息

   ```shell
   $ zcat SRR1039510_1_val_1.100000.fq.gz|paste - - - -| cut -f 1|wc -l
   25000
   ```

   因为每行开头为 "@SRR"

   ```shell
   $ zcat SRR1039510_1_val_1.100000.fq.gz|grep ^@SRR|wc -l
   25000
   ```

2. 输出所有的reads_1.fq文件中的标识符(即以@开头的那一行)

   ```shell
   $ zcat SRR1039510_1_val_1.100000.fq.gz|paste - - - -| cut -f 1|head
   @SRR1039510.1 HWI-ST177:290:C0TECACXX:1:1101:1373:2104 length=63
   @SRR1039510.2 HWI-ST177:290:C0TECACXX:1:1101:1340:2124 length=63
   @SRR1039510.3 HWI-ST177:290:C0TECACXX:1:1101:1273:2183 length=63
   @SRR1039510.4 HWI-ST177:290:C0TECACXX:1:1101:1562:2147 length=63
   @SRR1039510.5 HWI-ST177:290:C0TECACXX:1:1101:1577:2181 length=63
   @SRR1039510.6 HWI-ST177:290:C0TECACXX:1:1101:1650:2181 length=63
   @SRR1039510.7 HWI-ST177:290:C0TECACXX:1:1101:1645:2203 length=63
   @SRR1039510.8 HWI-ST177:290:C0TECACXX:1:1101:1597:2229 length=63
   @SRR1039510.9 HWI-ST177:290:C0TECACXX:1:1101:1791:2146 length=63
   @SRR1039510.10 HWI-ST177:290:C0TECACXX:1:1101:1902:2147 length=63
   ```

3. 输出reads_1.fq文件中的 所有序列信息(即每个序列的第二行)

   ```shell
   $ zcat SRR1039510_1_val_1.100000.fq.gz|paste - - - -| cut -f 2|head
   TGGGAGGCTGAGGCAGGAGAATCACTTAAACCTGGGAGGCAGAGGTTACAGTGAGCCGAGATT
   AAAGAAGGCGACAGTGAGAAGGAGTCCGAGAAGAGTGATGGAGACCCAATAGTCGATCCTGAG
   CTGCTGGGCCCCAAGGTCCTCCTGGTCCCAGTGGTGAAGAAGGAAAGAGAGGCCCTAATGGGG
   CTTGGCTGCAGCCATCCCGCTTAGCCTGCCTCACCCACACCCGTGTGGTACCTTCAGCCCTGG
   TGAGACAGGTAATTCAGTATAGTAGATTAATATTTTTAATATATATTTTCCCTTAAGATTTCC
   ATTTCTCAGTGTAGAAATCATGTCTTCTTAATTGCTGAACCTTACTGCAAAAACTTGTGATGT
   ATCAAGAATACCAAAACAGTTTCCTAATATACAGTATTTGAAAGTGCTTGCCATATTGGCTCT
   CTCATTTTCATCTTCACCATCAACAGAGAGAGCAGCATACTTGCTTGCAGAACTGAACTT
   TCCAACCGCAGCTTGGCATCTTCGGTGGCCTGCAGCTCGTCCTCCAGCTCTTCCAGCTGCGTC
   CGGCCTCCCAAAGTGCTGGGATTACAGGCATGAGCCACCGCGCTCTGCGAGGTACTTTTTCTA
   ```

4. 输出质量值信息(即每个序列的第四行)

   ```shell
   $ zcat SRR1039510_1_val_1.100000.fq.gz|paste - - - -| cut -f 4|head
   HJJJIJJJJJJJJIJJJGHHIJIIIIIIJJEHGGIJGIJIJJIJHHHGGFFDFFFDEDDDBDC
   HJJJJJJJJJJJIJIIGIJJJJGJHJJJHHDFFFE@CEEEDDDDDDDDDDDDDDDBDDDDDDD
   HJJJJJJJJJJJJJJJGIIIJJJJJHIJJJJHIJFHGIJJJJJJJHHHHHFFFDDDEDDDDDD
   HJJJJJJJJJJJJJJJJJIJJJJJJJJJJJJIJHJJIJJJJJHHFFFFEEEEEEEDDDDDDDB
   HIJJJJJJJEHJIJJJJIIIJJIIJJJJJJJJJJJJJJJJJJJJJJJJJEHJGI>FFCBGGGI
   HJJJJJJJJJJJHIJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJIJJJJJJJJHHIJJD
   FJGGJGIJJIIEIIJIIIGGGIGHIDGHGHGGCHFGHHBFGFBCCC4@AD@HIDIJJDHFFHH
   HIIIJJJJIIIIIJIJIGIIJJJJIJHIIIIIIGIGJJIIIIJJJJJIJJJJJJIGGIGJ
   HJJJJJJIJJJJJJJJJJJJJJJJJGIJJJIJJJJIJJJIJHHHGFFFFFEEEEEEDDDDDDB
   HJJJJIJJJJJJJDFGIJJJJJJJIJJJJJIIJFIJJJJJJJHFFFFEDDDD;CCDEEDDDDD
   ```

5. 统计文件中reads_1.fq文件里面的序列的碱基总数

   ```shell
   $ zless SRR1039510_1_val_1.100000.fq.gz |paste - - |cut -f 2|grep -io [ATCGN]|wc -w
   1665852
   ```

6. 统计reads_1.fq 中所有序列的第一位碱基的ATCGN分布情况(ATCGN分别个数)(uniq)

   ```shell
   $ zless -S SRR1039510_1_val_1.100000.fq.gz|paste - - - -|cut -f 2|grep -o ^[ACTGN]|sort|uniq -c
      5923 A
      6112 C
      5802 G
      7163 T
   ```

7. 将reads_1.fq 转为reads_1.fa文件(即将fastq转化为fast)(tr)

   ```shell
   $ zless -S SRR1039510_1_val_1.100000.fq.gz|paste - - - -|cut -f 1,2|tr '\t' '\n'|tr '@' '>' > ~/1_fasta
   $ cd ~
   $ head 1_fasta 
   >SRR1039510.1 HWI-ST177:290:C0TECACXX:1:1101:1373:2104 length=63
   TGGGAGGCTGAGGCAGGAGAATCACTTAAACCTGGGAGGCAGAGGTTACAGTGAGCCGAGATT
   >SRR1039510.2 HWI-ST177:290:C0TECACXX:1:1101:1340:2124 length=63
   AAAGAAGGCGACAGTGAGAAGGAGTCCGAGAAGAGTGATGGAGACCCAATAGTCGATCCTGAG
   >SRR1039510.3 HWI-ST177:290:C0TECACXX:1:1101:1273:2183 length=63
   CTGCTGGGCCCCAAGGTCCTCCTGGTCCCAGTGGTGAAGAAGGAAAGAGAGGCCCTAATGGGG
   >SRR1039510.4 HWI-ST177:290:C0TECACXX:1:1101:1562:2147 length=63
   CTTGGCTGCAGCCATCCCGCTTAGCCTGCCTCACCCACACCCGTGTGGTACCTTCAGCCCTGG
   >SRR1039510.5 HWI-ST177:290:C0TECACXX:1:1101:1577:2181 length=63
   TGAGACAGGTAATTCAGTATAGTAGATTAATATTTTTAATATATATTTTCCCTTAAGATTTCC
   ```

8. 删除 reads_1.fa文件每条序列的前后五个碱基

   ```shell
   # awk 里的if else并没有完全看懂
   $ less -S 1_fasta  |awk '{if($0~/^>/)print $0;else print substr($0,6,length($0)-10)}'|head
   >SRR1039510.1 HWI-ST177:290:C0TECACXX:1:1101:1373:2104 length=63
   GGCTGAGGCAGGAGAATCACTTAAACCTGGGAGGCAGAGGTTACAGTGAGCCG
   >SRR1039510.2 HWI-ST177:290:C0TECACXX:1:1101:1340:2124 length=63
   AGGCGACAGTGAGAAGGAGTCCGAGAAGAGTGATGGAGACCCAATAGTCGATC
   >SRR1039510.3 HWI-ST177:290:C0TECACXX:1:1101:1273:2183 length=63
   GGGCCCCAAGGTCCTCCTGGTCCCAGTGGTGAAGAAGGAAAGAGAGGCCCTAA
   >SRR1039510.4 HWI-ST177:290:C0TECACXX:1:1101:1562:2147 length=63
   CTGCAGCCATCCCGCTTAGCCTGCCTCACCCACACCCGTGTGGTACCTTCAGC
   >SRR1039510.5 HWI-ST177:290:C0TECACXX:1:1101:1577:2181 length=63
   CAGGTAATTCAGTATAGTAGATTAATATTTTTAATATATATTTTCCCTTAAGA
   ```

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
   
