和上次 [大概是用shiny写了个批量转换基因ID的网页]([https://www.jianshu.com/p/78ef37c5dfb1](https://www.jianshu.com/p/78ef37c5dfb1)
) ~~如出一辙~~  相比，粗暴解决了 **上传文件** or **直接输入** 的矛盾
不过缺点是，依然需要把gtf下载到本地，然后读入 Rstudio ,
(anyway 这个目前不造怎么解决
---
ui 长这样：
```R
library(shiny)

ui <- fluidPage(
  
  titlePanel("One Click Gencode Annotation"),
  
  sidebarLayout(
    sidebarPanel(
      
      fileInput("file1", "Choose file to upload",
                multiple = FALSE,
                accept = c('text/csv',
                           'text/comma-separated-values',
                           'text/tab-separated-values',
                           'text/plain',
                           '.csv')),
      
      helpText(strong("Please upload a gene list.")),
      helpText("Accepted formats: .txt, .csv"),
      hr(),
      
      h4("Or"),
      textInput("genesymbol", "Type a gene symbol/name",
                "TP53"),
      hr(),
      
      actionButton("goButton", "GO!", icon("paper-plane"),
                   style="color: #fff; background-color: #337ab7; 
                   border-color: #2e6da4")
    ),
    
    mainPanel(
      
      tabsetPanel(type = "tabs",
                  tabPanel("Your Genes", tableOutput("genelist")),
                  tabPanel("Gencode Annotation Info", tableOutput("annotationinfo"))
                  
      )
    )
  )
)
```
server 长这样：
```R
# url <- "ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_31/gencode.v31.annotation.gtf.gz"
# GRCh38p12 <- tempfile(fileext=".gtf.gz", tmpdir = ".")
# download.file(url, GRCh38p12)
# 
# gtf1 <- rtracklayer::import("file3a2017cb68c2.gtf.gz")
# gtf_df <- as.data.frame(gtf1)


server <- function(input, output) {
  
  output$genelist <- renderTable({
    if (is.null(input$file1))
      return(input$genesymbol)
    else {
      genelist <- read.csv(input$file1$datapath, 
                         header = FALSE, sep = "\t")
      colnames(genelist) <- "gene_name"
      genelist
    }
  })
  
   inputgene <- reactive({
     if (is.null(input$file1))
       return(input$genesymbol)
     else {
       genelist <- as.character(read.csv(input$file1$datapath, 
                                header = FALSE, sep = "\t")[,1])
       genelist
     }
  })
  
  
  output$annotationinfo <- renderTable({
    input$goButton
    annotable <- subset(gtf_df,gtf_df$gene_name==inputgene(), 
                        colnames(gtf_df))
    return(annotable)
  })
  
}
```
最后 RunApp 后就是这个亚子：
![image](https://upload-images.jianshu.io/upload_images/14383117-d2902b7ce11f6580.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
