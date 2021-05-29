> 啥也不懂，啥也不问
![](https://upload-images.jianshu.io/upload_images/14383117-144ee33a1f53a0f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

就放个ui代码吧
```R
library(shiny)
library(shinythemes)
library(DT)


ui <- fluidPage(theme = shinytheme("paper"),
                titlePanel("GSEA-KEGG"),
                hr(),
                h3("Get GSEA Info Table"),
                sidebarLayout(
                  
                  sidebarPanel(
                    fileInput("file1","Upload a RANKED GENE LIST (decreasing order) file",
                              multiple = FALSE,
                              accept = c('text/csv',
                                         'text/comma-separated-values',
                                         'text/tab-separated-values',
                                         'text/plain',
                                         '.csv')),
                    helpText("Note1. Your genelist should contain gene ID with corresponding numeric variable (e.g., FC)."),
                    helpText("Note2. The preferred format of gene ID is ENTREZID."),
                    radioButtons("categorycd", "Select a Category Code from ", 
                                 c("H"="hallmark","C1"="c1","C2"="c2","C3"="c3",
                                   "C4"="c4","C5"="c5","C6"="c6","C7"="c7"),
                                 selected = 'hallmark'),
                    br(),
                    textInput("annodb","Type your Annotation Package","org.Hs.eg.db"),
                    br(),
                    actionButton("action", "RUN"),
                    br(),br(),
                    actionButton("reset", "Rest the Category")
                  ),
                  
                  mainPanel(
                    tabsetPanel(type = "tabs", 
                                tabPanel("Your Genes", DT::dataTableOutput("genetable")),
                                tabPanel("MSigDb Table", DT::dataTableOutput("msigtable")),
                                tabPanel("KEGG pathways", DT::dataTableOutput("keggoutput")))
                    )
                  
                ),
                  
                hr(),
                h3("Pathview Download"),
                flowLayout(textInput("selectedpath","Type one KEGG ID","hsa00270"),
                br(),br(),
                actionButton("downloadimage", "Pathview Image",
                             icon("project-diagram")),
                hr()
                  
                )
)
```

![](https://upload-images.jianshu.io/upload_images/14383117-bb22f08b23634265.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

用`DOSE`的内置数据写出为`.txt`后重新读入
```R
data(geneList, package="DOSE")
write.table(geneList,file = "DOSE_geneList.txt", quote = F, col.names = F, sep = "\t")
```
![](https://upload-images.jianshu.io/upload_images/14383117-0e97b43d8d3bfe97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/14383117-9fc7fc230ad2ac8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/14383117-ece9bc0d4cc24cf9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点一下
![](https://upload-images.jianshu.io/upload_images/14383117-92cfb2e77390c787.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
get!
![](https://upload-images.jianshu.io/upload_images/14383117-1eb572c69080be7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
但发现一个问题：
```R
pathview(gene.data  = genelist(),
             pathway.id = input$selectedpath,
             species    = "hsa",
             limit      = list(gene=max(abs(genelist())), cpd=1),
             kegg.dir   = "../www")
```
设置图片储存路径后，`*.pathview.png`还是在工作路径中
![](https://upload-images.jianshu.io/upload_images/14383117-f8f448d6c70554d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
迷惑desu.

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
