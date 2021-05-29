> Fanta sea, totally
![](https://upload-images.jianshu.io/upload_images/14383117-380339663b0c9f64.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## shiny-seq 复现

> <https://www.rna-seqblog.com/shiny-seq-advanced-guided-transcriptome-analysis/>

之前的 [倒在boxplot前的失败经验](<https://www.jianshu.com/p/7ce3f155688e>) 

终于搞定了如何用 raw data (一个表达矩阵，一个有两种分组信息的annotation file) 画出 boxplot 和 PCA. 反正作者代码一环套一环是看不懂的 _(:з)∠)_

ui:

```R
ui <- navbarPage(title = "ShinySeq - Data to Plots",

           #### Raw Data ####
           tabPanel("Raw Data", 
                    fluidPage(theme = shinytheme("yeti"),
                           
                              h3(tags$b("Raw Data")),
                            
                              
                              sidebarLayout(
                                
                                sidebarPanel(
                                  fileInput("file1","Choose expression file to upload",
                                            multiple = FALSE,
                                            accept = c('text/csv',
                                                       'text/comma-separated-values',
                                                       'text/tab-separated-values',
                                                       'text/plain',
                                                       '.csv')),
                                  hr(),
                                  fileInput("file2","Choose annotation file to upload",
                                            multiple = FALSE,
                                            accept = c('text/csv',
                                                       'text/comma-separated-values',
                                                       'text/tab-separated-values',
                                                       'text/plain',
                                                       '.csv')),
                                  hr(),
                                  actionButton("raw_plot_run", "RUN")
                                ),
                                mainPanel(
                                  tabsetPanel(type = "tabs", 
                                              tabPanel("Expression", DT::dataTableOutput("expressionfile")),
                                              tabPanel("Annotaiton", DT::dataTableOutput("annotationfile")),
                                              tabPanel("Reshaped", DT::dataTableOutput("reshapedtable")))
                                )
                                
                              ),
                              hr(),
                              h3(tags$b("Plots")),
                              navlistPanel(
                                tabPanel("Box Plot", 
                                         tabsetPanel(type = "pills",
                                                     tabPanel("Phenotype",plotOutput("raw_Phenobox")),
                                                     tabPanel("Donor",plotOutput("raw_Donorbox"))
                                         )), 
                                
                                tabPanel("PCA", 
                                         tabsetPanel(type = "pills",
                                                     tabPanel("Phenotype",plotOutput("raw_PhenoPCA")),
                                                     tabPanel("Donor",plotOutput("raw_DonorPCA"))
                                         )
                              )
                              
                    )
           )),
           #### Normalized ####
           tabPanel("Normalized", 
                    fluidPage(theme = shinytheme("yeti"),
                              h3(tags$b("Normalized Data"))
                              ))
           )
```

server:

ps. 某两个嵌套for循环就已经耗尽了全部力气，但突然领悟了 `reactiveValues` 的真实方便

```R
# ====== shinytools 1 - Transcriptome Data Pre‑processing =====
## server


library(ggplot2)
library(reshape2)

server <- function(input, output) {

  output$expressionfile <-  DT::renderDataTable({
    if (is.null(input$file1))
      return(NULL)
    read.csv(input$file1$datapath, header = TRUE,sep = "\t")
    
  })
  
  output$annotationfile <- DT::renderDataTable({
    if (is.null(input$file2))
      return(NULL)
    read.csv(input$file2$datapath, header = TRUE,sep = "\t")
  })
  
  ###### boxplot #####
 
  
  values <- reactiveValues(
    exp=NULL,
    ann=NULL,
    expset=NULL,
    pca_data=NULL,
    pcx=NULL,
    pcr=NULL)
  
  
  observeEvent(input$raw_plot_run, {
    
    values$exp <- read.csv(input$file1$datapath, header = TRUE,sep = "\t",row.names = 1)
    values$ann <- read.csv(input$file2$datapath, header = TRUE,sep = "\t")
    values$exp <- values$exp[,values$ann$SampleID]
    values$expset <- melt(values$exp)
    
    for (j in 1:length(values$ann$SampleID)) {
      for (i in 1:length(unique(values$ann$Phenotype))) {
        values$expset$Phenotype <- rep(as.character(unique(values$ann$Phenotype)),
                                       each = as.numeric(table(values$expset$variable)[j])*as.numeric(table(values$ann$Phenotype)[i]))
      }
    }
    
    for (j in 1:length(values$ann$SampleID)) {
      for (i in 1:length(unique(values$ann$Donor))) {
        values$expset$Donor <- rep(as.character(unique(values$ann$Donor)),
                                   each = as.numeric(table(values$expset$variable)[j]),
                                   time = as.numeric(table(values$ann$Donor)[i]))
      }
    }
    
    
    output$reshapedtable <- DT::renderDataTable({
      values$expset
    })
   
    output$raw_Phenobox <- renderPlot({
      # waitress$start() # start the waitress
      ggplot(values$expset, aes(x=variable,y=value,fill=Phenotype))+geom_boxplot(outlier.shape = NA)+
        ylim(0,2000)+labs(x="Rows",y="Values") 
      # waitress$hide() # hide when done
      })
    
    output$raw_Donorbox <- renderPlot({
      # waitress$start() # start the waitress
      ggplot(values$expset, aes(x=variable,y=value,fill=Donor))+geom_boxplot(outlier.shape = NA)+
        ylim(0,2000)+labs(x="Rows",y="Values") 
      # waitress$hide() # hide when done
    })
    
    
    values$pca_data <- prcomp(t(values$exp),scale=TRUE)
    values$pcx <- data.frame(values$pca_data$x)
    values$pcr_p <- cbind(samples=rownames(values$pcx),values$ann$Phenotype, values$pcx) 
    values$pcr_d <- cbind(samples=rownames(values$pcx),values$ann$Donor, values$pcx)
    
    output$raw_PhenoPCA <- renderPlot({
      ggplot(values$pcr_p, aes(PC1, PC2)) + geom_point(size=5, aes(color = values$ann$Phenotype))
      
    })
    
    output$raw_DonorPCA <- renderPlot({
      ggplot(values$pcr_d, aes(PC1, PC2)) + geom_point(size=5, aes(color = values$ann$Donor))
      
    })
    
  })
}
```

发现一个看起来很高级的做过场渲染的包，`waiter` <https://github.com/JohnCoene/waiter> 试图使用失败

目前完成到了……这样👇

画不出和作者一样的图只是因为没设置ylim, 毕竟 raw data 就是 raw data, 动是动不起的

![](https://upload-images.jianshu.io/upload_images/14383117-eeebd45b0dd17883.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/14383117-bd1b5f29aa594f1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3D PCA hmmmm 画出来都得插会儿腰

---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)


