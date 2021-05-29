> 暴躁
![](https://upload-images.jianshu.io/upload_images/14383117-52512cf1b8be4503.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/14383117-58a766b8bcfa521f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)








> 基于课程《生物信息学之玩转 Linux》，由史上最大的生物信息学交流平台「生信技能树」联盟「丁香通」联合出品。



### 1. 云服务器

#### 从购买云服务器开始

阿里云24岁以下自动认证为学生，用半杯奶茶钱薅到一把羊毛┗|｀O′|┛ 

![](https://upload-images.jianshu.io/upload_images/14383117-a68be0a8e93aa3ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


连接后会出现一个网页版的终端。

查看配置：

```shell
df -h
```

![](https://upload-images.jianshu.io/upload_images/14383117-b9aca3814b2950fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以 G 为单位查看内存：

```shell
free -g
```

![](https://upload-images.jianshu.io/upload_images/14383117-0ce28dc1bb571197.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查看线程：

```shell
cat /proc/cpuinfo
```

![](https://upload-images.jianshu.io/upload_images/14383117-1c409fb2a98002da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Xshell 连接服务器

命令操作：

```shell
ssh username@public IP 
```

或直接在这里输入输入公网IP👇

![](https://upload-images.jianshu.io/upload_images/14383117-95da017894cd4cf7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在跳出的窗口内输入密码即可。

### 2. 文件目录操作

#### 目录结构

- `/` 根目录
- `/bin` 可执行二进制文件的目录
- `/etc` 系统配置文件存放的目录
- `/home` 用户家目录
- `/sbin` 系统管理员使用的可执行命令
- `/root` 存放启动 Linux 时使用的一些核心文件

![](https://upload-images.jianshu.io/upload_images/14383117-54c9f4fdd00c3ee9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 目录常用命令

- `dirs` 显示目录记录

- `rmdir` 删除空目录

- `mkdir` 创建目录 (make a new directory

- `rm` 删除给定的文件和目录 (remove (unlink) files or directories

- `pwd` 显示当前工作目录的绝对路径

- `ls` 显示目录内容列表 (list and generate statistics for files

- `mv` 对文件或目录重命名 (move or rename files

  mv file file, 重命名

  mv file path, 移动

- `cp` 将源文件或目录复制到目标文件或目录中 (copy file data

- `cd` 切换用户当前工作目录 (change current directory

#### 文件命令

- `cat` 查看文件内容 (concatenate and print data
- `less` 分屏上下翻页浏览文件内容
- `more` 显示文件内容，每次显示一屏 (display file data at your terminal
- `head` 查看文件前几行 (give first few lines, default: n = 10
- `tail` 查看结尾几行 (print last lines from file
- `diff` 查看两个文件是否有差异 (differential file comparator
- `find` 查找文件
- `touch` 生成一个空文件或更改文件时间 (update access and modification times of a file
- `ln` 建立链接文件
- `file/stat` 查看文件类型或文件属性信息
- `echo` 把内容重定向到指定的文件中
- `wc` 统计文件中字节数 (count words, lines, and characters

#### 练习题

1. 在任意文件夹下面创建形如 `1/2/3/4/5/6/7/8/9` 格式的文件夹系列

2. 在创建好的文件夹下，创建文本文件 `me.txt` 

3. 在文本文件 `me.txt` 里面输入内容

   ```markdown
   Go to: htttp://www.biotrainee.com/
   I love bioinformatics.
   And you?
   ```

   ```shell
   mkdir -p 1/2/3/4/5/6/7/8/9
   cd 1/2/3/4/5/6/7/8/9
   touch me.txt
   vi me.txt
   ```

   `:wq` 退出

   ![](https://upload-images.jianshu.io/upload_images/14383117-f66e5498dc337239.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 删除上面创建的文件夹及文件

   ```shell
   cd -
   rm -r 1/2/3/4/5/6/7/8/9
   ```

5. 在任意文件夹下面创建 folder `1-5` 这5个文件夹，再继续在每个文件夹下面创建 folder `1-5` 这5个文件夹

   ```R
   mkdir folder{1..5}
   touch folder{1..5}/me.txt
   ```

   ![](https://upload-images.jianshu.io/upload_images/14383117-1b9877f923269cc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 再次删除

   ```shell
   rm -r folder{1..5}
   ```

### 3. 绝对路径与相对路径

- 绝对路径

  路径由根目录 `/` 

- 相对路径

  `.` 当前目录

  `..` 上级目录

  `-` 前一个目录

  `~` 当前用户所在的主文件夹

即绝对路径是某个文件/文件夹固定的所在位置，相对路径是站在当前所在位置视角进行文件夹切换等任务。

> An **absolute path** refers to the complete details needed to locate a file or folder, starting from the root element and ending with the other subdirectories. Absolute paths are used in websites and operating systems for locating files and folders.
>
> An absolute path is also known as an **absolute pathname** or **full path**.
>

> The path with reference to root directory is called **absolute**. The path with reference to current directory is called **relative**.
>

### 4. 通配符规则

| 符号 | 含义                       | 举例             |
| ---- | -------------------------- | ---------------- |
| *    | 代表任意（0到多个）字符    | *.fastq          |
| ?    | d代表任意1个字符           | ???.sh           |
| $    | 变量前需要加的符号         |                  |
| {}   | 命令区块组合或者内容序列   | mkdir 2016{a..z} |
| []   | 匹配 [] 中任意一单一字符   | a[xyz]b          |
| [!]  | 匹配除 [] 中任意一单一字符 | a[!0-9]b         |

`*` `?`


```shell
# 先创建几个文件
touch a.fastq aa.fastq abc.fastq
touch a.sh aa.sh abc.sh
```

![](https://upload-images.jianshu.io/upload_images/14383117-a84462bfd05a541c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/14383117-7ed590fb832984af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`$`

```shell
touch 1.pdf
a=1.pdf
ls $a
```

![](https://upload-images.jianshu.io/upload_images/14383117-b1ac329535e06eec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`{}`

![](https://upload-images.jianshu.io/upload_images/14383117-07bd5bca0ad96739.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`[]`

```shell
mkdir {a..e}
mkdir a{1..5}c
```

![](https://upload-images.jianshu.io/upload_images/14383117-a31296e3db139cfb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 5. 文本处理

#### 表格化操作

| 命令  | 释义                                     |
| ----- | ---------------------------------------- |
| cut   | 显示行中的指定部分，删除文件中指定字段   |
| tr    | 对来自标准输入的字符进行替换、压缩和删除 |
| sort  | 将文本文件内容进行排序                   |
| uniq  | 报告或忽略文件中的重复行                 |
| paste | 合并多个文件（按行）                     |

`cut`

> **参数:**
>
> - -b ：以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了 -n 标志。
> - -c ：以字符为单位进行分割。
> - -d ：自定义分隔符，默认为制表符。
> - -f ：与-d一起使用，指定显示哪个区域。
> - -n ：取消分割多字节字符。仅和 -b 标志一起使用。如果字符的最后一个字节落在由 -b 标志的 List 参数指示的范围之内，该字符将被写出；否则，该字符将被排除

![](https://upload-images.jianshu.io/upload_images/14383117-28fa34e0356118ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```shell
cut -c 3  test.txt
```

![](https://upload-images.jianshu.io/upload_images/14383117-7520c15ae004069c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`tr`

```shell
# 新建一个文本
# 把所有的A替换为G
cat expnt |tr A G
```

![](https://upload-images.jianshu.io/upload_images/14383117-a38a2258f9349a03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`sort`

```shell
# 新建一个文本
# sort 命令将以默认的方式将文本文件的第一列以ASCII 码的次序排列，并将结果输出到标准输出
sort sortexp
```

![](https://upload-images.jianshu.io/upload_images/14383117-c129680777af2bbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`uniq`

> **参数**：
>
> - -c或--count 在每列旁边显示该行重复出现的次数。

```shell
uniq expnt
uniq -c expnt
```

![](https://upload-images.jianshu.io/upload_images/14383117-7fa4d2f39af924b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`paste`

```shell
paste expnt sortexp
```

![](https://upload-images.jianshu.io/upload_images/14383117-fb79ed2cba56574a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

随便创建的文本并不整齐，就大概体验一下o_o ....

#### 三驾马车

| **命令** | **释义**                   |
| -------- | -------------------------- |
| grep     | 查找文件中符合条件的行     |
| awk      | 对特定的行中特定列进行操作 |
| sed      | 文本处理并可对文件进行编辑 |

`grep`

```shell
grep pts 1.txt
grep pts 1.txt|cut -d/ -f 2
```

![](https://upload-images.jianshu.io/upload_images/14383117-d9833b6a98da6cd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`awk`

```shell
# 按列输出
grep pts 1.txt|awk '{print $1}'
grep pts 1.txt|awk '{print $3}'
```

![](https://upload-images.jianshu.io/upload_images/14383117-9cf0c3ff89bf7c18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`sed`

下载 hg38.tss 进行操作

```shell
wget http://www.biotrainee.com/jimmyzeng/tmp/hg38.tss
# 找 tp53 对应的 RefSeq
grep NM_000546 hg38.tss
```

![](https://upload-images.jianshu.io/upload_images/14383117-eebaa79f0f05ec51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```shell
# 去除"chr"
head hg38.tss > tmp
sed 's/chr//g' tmp
```

![](https://upload-images.jianshu.io/upload_images/14383117-8ff3eae339588ca8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6. 压缩及解压

#### gz 

压缩：

```shell
gzip filename
```

解压：

```shell
gzip -d filename.gz
gunzip filename.gz
```

#### tar.gz

压缩：

```shell
tar -zcvf filename.tar.gz
```

解压：

```shell
tar -zxvf filename.tar.gz
```

#### bz2

压缩：

```shell
bzip2 filename
```

解压：

```shell
bzip2 -d filename.bz2
bunzip2 filename.bz2
```

![](https://upload-images.jianshu.io/upload_images/14383117-efefe095e649fc90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不同方法压缩得到的文件大小不同（不过不造为什么会是2017...

### 7. 快捷方式和环境变量

#### 快捷方式

先递归建立一堆文件夹：

```shell
mkdir -p 1/2/3/4/5/6/7/8/9
ln -s 1/2/3/4/5/6/7/8/9 ./
# 把快捷方式创建在当前目录下
```

![](https://upload-images.jianshu.io/upload_images/14383117-04d2a05498892084.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

文件软连接/快捷方式

```shell
ln -s 1.txt 2.txt
```

![](https://upload-images.jianshu.io/upload_images/14383117-ea5abc9441e609b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 环境变量

实际上 `ll` 是通过 `alias` 定义的：

![](https://upload-images.jianshu.io/upload_images/14383117-5878a6b7b674e3d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将 "ls -lh" 定义为 "lh"

```shell
alias lh="ls -lh"
```

但下次连接服务器时需要重新设置。

修改环境变量：

```shell
vim ~/.bashrc
```

在任意行放入 `alias lh="ls -lh"`

`:wq` 退出。

```shell
source ~/.bashrc
```
## Reference

- Linux Shell Commands <https://docs.cs.cf.ac.uk/notes/linux-shell-commands/>
- https://www.techopedia.com/definition/5817/absolute-path
- [Difference between Relative path and absolute path in javascript](https://stackoverflow.com/questions/21306512/difference-between-relative-path-and-absolute-path-in-javascript) shiva

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
