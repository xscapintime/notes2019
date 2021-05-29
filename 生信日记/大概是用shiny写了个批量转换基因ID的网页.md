> 成功的那一刻：
cry babe cry
![](https://upload-images.jianshu.io/upload_images/14383117-67561c4716ea548f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一开始设想得很宏大，最后.....就先杰样吧。
**ui** 长这样：
```r
library(shiny)

ui <- fluidPage(
  
  titlePanel("One Click Gene Annotation"),
  
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
      
      radioButtons("reference", "Select a reference genome:",
                   c("Human (Homo sapiens)" = "hs",
                     "Mouse (Mus Musculus)" = "mm"),
                   selected = 'hs'),
      hr(),
      
      actionButton("goButton", "GO!", icon("paper-plane"),
                   style="color: #fff; background-color: #337ab7; 
                   border-color: #2e6da4")
    ),
    
    mainPanel(
      
      tabsetPanel(type = "tabs",
                  tabPanel("Your Genes", tableOutput("genelist")),
                  tabPanel("Converted IDs", tableOutput("annotationinfo"))
        
      )
    )
  )
)
```

**server** 长这样：
```r
suppressPackageStartupMessages(library(org.Hs.eg.db))
suppressPackageStartupMessages(library(org.Mm.eg.db))
suppressPackageStartupMessages(library(AnnotationDbi))


server <- function(input, output) {
  
  output$genelist <- renderTable({
    
    if (is.null(input$file1))
      return(NULL)
    
    urgene <- read.csv(input$file1$datapath, 
                       header = FALSE, sep = "\t")
    colnames(urgene) <- "SYMBOL"
    urgene
     
  })
  
  annodb <- reactive({
    
    if(input$reference == "hs"){
      require(org.Hs.eg.db)
      annodb <- org.Hs.eg.db
    } else {
      (input$reference == "mm")
      require(org.Mm.eg.db)
      annodb <- org.Mm.eg.db
    } 
  
  })
  
  
  mykey <- reactive({
    
    req(input$file1)
    
    ks <- as.character(read.csv(input$file1$datapath, 
                                header = FALSE, sep = "\t")[,1])
    return(ks)
  })
  
  

  output$annotationinfo <- renderTable({
       
    input$goButton
    
    AnnotationDbi::select(
      annodb(),
      keys = mykey(),
      column = c("ENTREZID", "ENSEMBL", 
        "UNIGENE", "UNIPROT", "GENENAME"),
      keytype = "SYMBOL")
    
  })
  
}
```
Run APP 就会是这样：
![](https://upload-images.jianshu.io/upload_images/14383117-c51109a4c0141738.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)
