> 搞出来一个加拿大人民喝过的酒的交互可视化统计
![](https://upload-images.jianshu.io/upload_images/14383117-ad62c8f13f0b27a4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# NOTE ON *Building Shiny apps - an interactive tutorial*
**YOU DO NOT NEED TO KNOW ANY  HTML/CSS/JavaScript**

> <https://deanattali.com/blog/building-shiny-apps-tutorial/>

## 1. Shiny app basics

Every Shiny app is composed of **2 parts**:

- a web page
- a computer

In Shiny terminology，

- UI (user interface)
- server

**UI** 

- a web document that the user gets to see

- responsible for 
  - creating the layout of the app 
  - telling Shiny exactly where things go

**server**

- responsible for the logic of the app
- the set of instructions that tell the web page what to show

## 2. An empty Shiny app

```R
library(shiny)
ui <- fluidPage()
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

All Shiny apps follow the **same** template 👆

>  A few things you should keep in mind:
>
> - It is **very important** that the name of the file is `app.R`, otherwise it would not be recognized as a Shiny app.
> - You should not have any R code after the `shinyApp(ui = ui, server = server)` line. That line needs to be **the last line** in your file.
> - It is good practice to place this app in its **own folder**, and not in a folder that already has other R scripts or files, unless those other files are used by your app.

![](https://upload-images.jianshu.io/upload_images/14383117-19fa8c0f982ae650.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
runApp()
Listening on http://127.0.0.1:5360
```

Click the **stop** button to stop the app, or press the ***Escape*** key.

### 2.1 Alternate way: separate UI and server files

When the app is complex and involves more code,

**separate** the UI and server code into two files: 

- `ui.R`

- `server.R`

When RStudio sees these **two files in the same folder**, it will know you’re writing a Shiny app.

Do not need to include the `shinyApp(ui = ui, server = server)` line.

![](https://upload-images.jianshu.io/upload_images/14383117-b547c1fe7370edf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/14383117-c8976d87572c61c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2 Let RStudio fill out a Shiny app template 

![](https://upload-images.jianshu.io/upload_images/14383117-ebf9650fdbcc62e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
#
# This is a Shiny web application. You can run the application by clicking
# the 'Run App' button above.
#
# Find out more about building applications with Shiny here:
#
#    http://shiny.rstudio.com/
#

library(shiny)

# Define UI for application that draws a histogram
ui <- fluidPage(

    # Application title
    titlePanel("Old Faithful Geyser Data"),

    # Sidebar with a slider input for number of bins 
    sidebarLayout(
        sidebarPanel(
            sliderInput("bins",
                        "Number of bins:",
                        min = 1,
                        max = 50,
                        value = 30)
        ),

        # Show a plot of the generated distribution
        mainPanel(
           plotOutput("distPlot")
        )
    )
)

# Define server logic required to draw a histogram
server <- function(input, output) {

    output$distPlot <- renderPlot({
        # generate bins based on input$bins from ui.R
        x    <- faithful[, 2]
        bins <- seq(min(x), max(x), length.out = input$bins + 1)

        # draw the histogram with the specified number of bins
        hist(x, breaks = bins, col = 'darkgray', border = 'white')
    })
}

# Run the application 
shinyApp(ui = ui, server = server)
```

## 3. Load the dataset

就直接用作者的测试数据吧 [raw data](http://pub.data.gov.bc.ca/datasets/176284/BC_Liquor_Store_Product_Price_List.csv), 大概就是想从国企报表里康一康大家喝了多少酒什么酒花了多少钱这个亚子。
![](https://upload-images.jianshu.io/upload_images/14383117-a78c1900f73c3ca7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```R
bcl <- read.csv("bcl-data.csv", stringsAsFactors = FALSE)
## you will see a summary of the dataset which should let you know that the dataset was indeed loaded correctly
print(str(bcl))
# 'data.frame':	6132 obs. of  7 variables:
#  $ Type           : chr  "WINE" "WINE" "WINE" "WINE" ...
#  $ Subtype        : chr  "TABLE WINE RED" "TABLE WINE WHITE" "TABLE WINE RED" "TABLE WINE WHITE" ...
#  $ Country        : chr  "CANADA" "CANADA" "CANADA" "CANADA" ...
#  $ Name           : chr  "COPPER MOON - MALBEC" "DOMAINE D'OR - DRY" "SOMMET ROUGE" "MISSION RIDGE - PREMIUM DRY WHITE" ...
#  $ Alcohol_Content: num  14 11.5 12 11 13.5 11 12.5 12 11.5 40 ...
#  $ Price          : num  31 33 30 34 37 ...
#  $ Sweetness      : int  0 0 0 1 0 0 0 0 0 NA ...
# NULL
```

做个练习：

> **Exercise:** Load the data file into R and get a feel for what’s in it. How big is it, what variables are there, what are the normal price ranges, etc.

```R
dim(bcl)
# [1] 6132    7

## Alcohol Content
alc_mean <- mean(bcl$Alcohol_Content)
alc_mean
# [1] 17.16615
alc_max <- max(bcl$Alcohol_Content)
alc_max
# [1] 75.5
alc_min <- min(bcl$Alcohol_Content)
alc_min
# [1] 2.5
```

写个函数求众数：

```R
getthemost <- function(x) {
  tail(sort(table(x)), n=1)
}
alc_mode <- getthemost(bcl$Alcohol_Content)
alc_mode
#  13 
# 799
```

```R
## Price
price_mean <- mean(bcl$Price, na.rm = TRUE)
price_mean
# [1] 141.4914
price_max <- max(bcl$Price, na.rm = TRUE)
price_max
# [1] 30250
price_min <- min(bcl$Price, na.rm = TRUE)
price_min
# [1] 1.99
price_mode <- getthemost(bcl$Price)
price_mode
# 14.99 
#   217 
```

```R
getthetops <- function(x) {
  tail(sort(table(x)), n=5)
}
```

```R
## Country
topcountries <- getthetops(bcl$Country)
topcountries
# x
#                AUSTRALIA                    ITALY 
#                      364                      570 
# UNITED STATES OF AMERICA                   FRANCE 
#                      703                     1357 
#                   CANADA 
#                     1374 
```

```R
## Type 后面发现其实一共也就这四种
toptypes <- getthetops(bcl$Type)
toptypes
# x
# REFRESHMENT        BEER     SPIRITS        WINE 
#         111         683        1147        4191 
```

```R
## Subtype
topsubts <- getthetops(bcl$Subtype)
topsubts
# x
# SPARKLING WINE WHITE        SCOTCH - MALT                 # BEER 
#                  181                  208                  689 
#     TABLE WINE WHITE       TABLE WINE RED 
#                 1119                 2564 
```

看了半天，回到 shiny

## 4. Build the basic UI

### 4.1 Add plain text to the UI

**The first thing** you do when writing a Shiny app - **add elements** to the UI.

Place R strings inside `fluidPage()` to render text: 

```R
ui <- fluidPage("BC Liquor Store", "prices")
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-d8448ab6205879c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一步两步、

```R
ui <- fluidPage("BC Liquor Store", "prices", "Alcohol Content")
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-ce08bccc21d8a2da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.2 Add formatted text and other HTML elements

- `h1()` : a top-level header 
- `h2()` : a secondary header
- `strong()` : make text bold
- `em()` : make text italicized
- `br()` : a line break
- `img()` : an image
- `a()` : a hyperlink

```R
ui <- fluidPage(h1("My app"),
                "BC",
                "Liquor",
                br(),
                "Store",
                strong("prices"))
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-f78bce5e35c5aae4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.3 Add a title

Shiny also has a **special** function `titlePanel()` .

Using `titlePanel()` not only adds a visible big title-like text to the top of the page, but it also sets the “**official**” title of the web page.

```R
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"))
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-f5c95e8d6fc3b2c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.4 Add a layout

Use `sidebarLayout()` to add a simple structure.

```R
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"), 
                sidebarLayout(
                  sidebarPanel("our inputs will go here"),
                  mainPanel("the results will go here")
                ))
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-4231977c71f89651.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```R
## 随便试一下另外两个函数
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"), 
                sidebarLayout(
                  sidebarPanel("our inputs will go here"),
                  mainPanel("the results will go here")),
                column(width = 5, "5_1", offset = 3),
                column(width = 4, "4_1", offset = 3),
                fluidRow(
                  column(width = 5, "5_2"),
                  column(width = 4, "4 offset 2", offset = 2)
                ))
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-84ed944512b20df8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5. Add inputs to the UI

**Inputs** are what gives users a way to interact with a Shiny app.

- `textInput()` : enter text
- `numericInput()` : select a number
- `dateInput()` : selecta date
- `selectInput()` : create a select box (aka a dropdown menu)

All input functions have the **same first two arguments**: `inputId` and `label`.

- The `inputId` will be the **name**. Every input must have a unique `inputId`.
- The `label` argument **specifies the text** in the display label that goes along with the input widget.

```R
## 随便瞎试一下
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"),
                sidebarLayout(
                  sidebarPanel(
                    numericInput("Price",
                                 "in USD",
                                 "1.99",
                                 min = 0,
                                 max = NA)),
                  mainPanel("the results will go here")))
```

![](https://upload-images.jianshu.io/upload_images/14383117-675bd1d65fa4a822.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.1 Input for price

The first input we want to have is for specifying a price **range** (minimum and maximum price). The most sensible types of input for this are either `numericInput()` or `sliderInput()`.

- `numericInput()`, two inputs, one for the minimum value and one for the maximum. 
- `sliderInput()`, by supplying a vector of length two as the `value` argument, it can be used to specify a range rather than a single number. 

说了这么多，就是 `sliderInput()` 的效果更好，抛弃前面的 `numericInput()`.

前面求出最贵的酒是30250刀，显然买不起，买不起**我可以假装看不见**，所以把最大值设为100——当然，从四分位数也可以看出👇

```R
fivenum(bcl$Price)
# [1]     1.99    14.99    24.99    62.99 30250.00
```

最大值设为100是很合理的。

```R
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"),
                sidebarLayout(
                  sidebarPanel(
                    sliderInput("priceInput", "Price", 
                            min = 0, 
                            max = 100,
                            value = c(25, 40), 
                            pre = "$")),
                  mainPanel("the result will go here")))
```

![](https://upload-images.jianshu.io/upload_images/14383117-8f10931d3eb8603c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5.2 Input for product type

We could either use **radio buttons** or a **select box** for our purpose. Let’s use radio buttons for now since there are only a few options.

```R
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"),
                sidebarLayout(
                  sidebarPanel(
                    sliderInput("priceInput", "Price", 
                                min = 0, 
                                max = 100,
                                value = c(25, 40), 
                                pre = "$"),
                    radioButtons("typeInput", "Product type",
                                 choices = c("BEER", "REFRESHMENT", 
                                             "SPIRITS", "WINE"),
                                 selected = "WINE")),
                  mainPanel("the result will go here")))
```

![](https://upload-images.jianshu.io/upload_images/14383117-51cf010654b285bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.3 Input for country

红酒就要喝 **fà国** 的。

`selectInput()` : create a select box.

```R
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"),
                sidebarLayout(
                  sidebarPanel(
                    sliderInput("priceInput", "Price", 
                            min = 0, 
                            max = 100,
                            value = c(25, 40), 
                            pre = "$"),
                    radioButtons("typeInput", "Product type",
                                          choices = c("BEER", "REFRESHMENT", 
                                                      "SPIRITS", "WINE"),
                                          selected = "WINE"),
                    selectInput("countryInput", "Country",
                            choices = c("CANADA", "FRANCE", "UNITED STATES OF AMERICA",
                                        "ITALY", "AUSTRALIA"))),
                  mainPanel("the result will go here")))
```

![](https://upload-images.jianshu.io/upload_images/14383117-16ec9fbc3efe7e78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时候的网页已经有点人样了(雾，输入部分已完成，把所有模块整理好应该是这样的：

```R
bcl <- read.csv("bcl-data.csv", stringsAsFactors = FALSE)
library(shiny)
ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"),
                sidebarLayout(
                  sidebarPanel(
                    sliderInput("priceInput", "Price", 
                            min = 0, 
                            max = 100,
                            value = c(25, 40), 
                            pre = "$"),
                    radioButtons("typeInput", "Product type",
                                          choices = c("BEER", "REFRESHMENT", 
                                                      "SPIRITS", "WINE"),
                                          selected = "WINE"),
                    selectInput("countryInput", "Country",
                            choices = c("CANADA", "FRANCE", "UNITED STATES OF AMERICA",
                                        "ITALY", "AUSTRALIA"))),
                  mainPanel("the result will go here")))
server <- function(input, output) {}
shinyApp(ui = ui, server = server)
```

## 6. Add placeholders for outputs

Outputs can be any object that R creates and that we want to display in our app - such as a **plot**, a **table**, or **text**. 

All the ouput functions have a `outputId` argument that is used to identify each output, and this argument must be **unique** for each output.

这时候可以把"the result will go here"用代码替换了。

We’ll have a **plot** showing some visualization of the results.

We will have a **table** that shows all the results. To get a table, we use the `tableOutput()` function.

所以 `mainPanel()` 内部是这样的：

```R
mainPanel(
    plotOutput("coolplot"),
    br(), br(),
    tableOutput("results"))
```

## 7. Implement server logic to create outputs

Now we have to write the `server` function, which will be responsible for listening to changes to the inputs and **creating outputs** to show in the app.

Server function is always defined with two arguments: `input` and `output`.

- `input` : is a list you will **read** values *from* and will contain the values of all the different inputs at any given time.

- `output` is a list you will write values *to*, and it is where you will **save** output objects (such as tables and plots) to **display** in your app.

There are three rules to build an output in Shiny.

1. Save the output object into the `output` list (remember the app template - every server function has an `output` argument)
2. Build the object with a `render*` function, where `*` is the type of output
3. Access input values using the `input` list (every server function has an `input`argument)

### 7.1 Making an output react to an input

**2 rules:** 

1. we’re creating a plot **inside** the `renderPlot()`function, and assigning it to *coolplot* in the `output` list. 
2. Remember that every output created in the UI must have a **unique** ID, now we see why. In order to attach an R object to an output with ID *x*, we assign the R object to `output$x`.

```R
server <- function(input, output) {
  output$coolplot <- renderPlot({
    plot(rnorm(input$priceInput[1]))
  })
}
```

The variable `input` contains a list of all the inputs that are defined in the UI. `input$priceInput` return **a vector of length 2** containing the miminimum and maximum price. 

We are **saving** to the `output` list (`output$coolplot <-`), we are using a `render*` function to **build** the output (`renderPlot({})`), and we are **accessing** an input value (`input$priceInput[1]`).

![](https://upload-images.jianshu.io/upload_images/14383117-4f46c1a2c6d4cc37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.2 Building the plot output

上面的只是试手，**正式的**——用 `ggplot2` 画一个酒精含量的直方图。

```R
# library(ggplo2)
server <- function(input, output) {
  output$coolplot <- renderPlot({
    ggplot(bcl, aes(Alcohol_Content)) + 
      geom_histogram()
  })
}
```

![](https://upload-images.jianshu.io/upload_images/14383117-22cc8486213cbc0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时候的图是不随旁边的输入数据改变的，所以 the next step is to actually filter the dataset based on the inputs.

```R
# library(ggplot2)
# library(dplyr)

server <- function(input, output) {
  output$coolplot <- renderPlot({
    filtered <- 
      bcl %>% 
      filter(Price >= input$priceInput[1],
             Price <= input$priceInput[2],
             Type == input$typeInput,
             Country == input$countryInput)
    ggplot(filtered, aes(Alcohol_Content)) +
      geom_histogram()
  })
}
```

![](https://upload-images.jianshu.io/upload_images/14383117-fd68e34804a341ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7.3 Building the table output

The other output we have was called `results` (as defined in the UI) and should be a table of all the products that match the filters.

We should use the `renderTable()`function. We’ll do the **exact same filtering** on the data, and then simply return the data as a data.frame.

在 `server <- function(input, output) {} `下加上：

```R
output$results <- renderTable({
    filtered <- 
      bcl %>% 
      filter(Price >= input$priceInput[1],
             Price <= input$priceInput[2],
             Type == input$typeInput,
             Country == input$countryInput)
```



![](https://upload-images.jianshu.io/upload_images/14383117-2a0b4a85e14e5457.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 8. Reactivity

Shiny uses a concept called **reactive programming**. This is what enables your outputs to *react* to changes in inputs. 

Only *reactive* variables behave this way, and in Shiny all inputs are automatically reactive. 

### 8.1 Creating and accessing reactive variables

One very important thing to remember about reactive variables (such as the `input` list) is that **they can only be used inside reactive contexts**. 

Any `render*` function is a reactive context, so you can always use `input$x` or any other reactive variable **inside** render functions. 

2 other common reactive contexts:

- `reactive({})` 
- `observe({})`

`observe({})` statement *depends* on `input$priceInput`.

Remember to **wrap** the `cat(input$x)` or `print(input$x)` by an `observe({})`.

在 `server <- function(input, output) {} `下加上：

```R
observe({ print(input$priceInput) })
```

You can also **create** your own reactive variables using the `reactive({})` function.

The **difference** between `reactive({})` and `observe({})` is that `reactive({})` returns a **value**.

Create a variable called `priceDiff` that will be the **difference** between the maximum and minimum price selected.

在 `server <- function(input, output) {} ` 下加上：

```R
priceDiff <- reactive({
  diff(input$priceInput)
})
observe({print(priceDiff())})
```

If you want to access a reactive variable defined with `reactive({})`, **you must add parentheses after the variable name, as if it’s a function**. 

### 8.2 Using reactive variables to reduce code duplication

again, 上面的都是试手。

we have the exact **same** code filtering the dataset in two places, once in each render function. We can solve that problem by **defining a reactive variable** that will hold the filtered dataset, and use that variable in the render functions.

在 `server <- function(input, output) {} ` 下加上：

```R
filtered <- reactive({
  bcl %>%
    filter(Price >= input$priceInput[1],
           Price <= input$priceInput[2],
           Type == input$typeInput,
           Country == input$countryInput
    )
})
```

将 **7.2** 和 **7.3** 的代码换成：

```R
output$coolplot <- renderPlot({
    ggplot(filtered(), aes(Alcohol_Content)) +
      geom_histogram()
})

output$results <- renderTable({
    filtered()
})
```

## 9. Using uiOutput() to create UI elements dynamically

### 9.1 Basic example of uiOutput()

One of the **output functions** you can add in the UI is `uiOutput()`,  this is an output used to render more UI. It’s usually used to **create inputs** (or any other UI) from the server.

```R
ui <- fluidPage(
  numericInput("num", "Maximum slider value", 5),
  uiOutput("slider")
)

server <- function(input, output) {
  output$slider <- renderUI({
    sliderInput("slider" , "Slider", min = 0,
                max = input$num , value = 0)
  })
}

shinyApp(ui = ui, server = server)
```

![](https://upload-images.jianshu.io/upload_images/14383117-d7bcb3043dcf1194.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

slider 的最大值是随着 input 变动的。

### 9.2 Use uiOutput() in our app to populate the countries

现在通过 `uiOutput()` `renderUI({})` 这个组合把对国家的选择放在 `server <- function(input, output) {} ` 下。

把 `ui <- fluidPage()` 下的 `selectInput("countryInput", ...)` 换成：

```r
uiOutput("countryOutput")
```

在 `server <- function(input, output) {} ` 下加上：

```R
output$countryOutput <- renderUI({
    selectInput("countryInput", "Country",
                sort(unique(bcl$Country)),
                selected = "CANADA")
```

这样就可以选择所有国家了。

![](https://upload-images.jianshu.io/upload_images/14383117-c2c24a89bd84c80e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 9.3 Errors showing up and quickly disappearing

1. The problem is that when the app initializes, `filtered` is trying to access the country input, but the country input hasn’t been created yet.

   Inside the `filtered` reactive function, we should check if the country input exists, and if not then just return `NULL`.

2. The ggplot function will not work with a `NULL` dataset, so we also need to make a similar check in the `renderPlot()` function.

修复一闪而过的报错，在 `filtered <- reactive({})` 下加上：

```R
if (is.null(input$countryInput)) {
    return(NULL)
}    
```

`output$coolplot <- renderPlot({})` 下加上：

```R
if (is.null(filtered())) {
      return()
}
```

## FINALLY

大致粗糙做完了，现在这个网页长这样：

![](https://upload-images.jianshu.io/upload_images/14383117-9f2d3cf34d4d01be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**ui.R** 长这样：

```r 
## bc_liquor_store_ui

bcl <- read.csv("bcl-data.csv", stringsAsFactors = FALSE)
library(shiny)

ui <- fluidPage(titlePanel("BC Liquor Store prices", 
                           windowTitle = "BC Liquor Store prices"),
                sidebarLayout(
                  sidebarPanel(
                    sliderInput("priceInput", "Price", 
                                min = 0, 
                                max = 100,
                                value = c(25, 40), 
                                pre = "$"),
                    radioButtons("typeInput", "Product type",
                                 choices = c("BEER", "REFRESHMENT", 
                                             "SPIRITS", "WINE"),
                                 selected = "WINE"),
                    uiOutput("countryOutput")),
                  mainPanel(plotOutput("coolplot"),
                            br(), br(),
                            tableOutput("results")
                  )))
```

**server.R** 长这样：

```R
## bc_liquor_store_server

library(ggplot2)
library(dplyr)

server <- function(input, output) {
  filtered <- reactive({
    if (is.null(input$countryInput)) {
      return(NULL)
    }    
    bcl %>% 
      filter(Price >= input$priceInput[1],
             Price <= input$priceInput[2],
             Type == input$typeInput,
             Country == input$countryInput)
  })
  
  output$coolplot <- renderPlot({
    if (is.null(filtered())) {
      return()
    }
    ggplot(filtered(), aes(Alcohol_Content)) +
      geom_histogram()
  })
  
  output$results <- renderTable({
    filtered()
  })
  
  output$countryOutput <- renderUI({
    selectInput("countryInput", "Country",
                sort(unique(bcl$Country)),
                selected = "CANADA")
  })
}
```
---
# 最后，向大家隆重推荐生信技能树的一系列干货！

1.  生信技能树全球公益巡讲：[https://mp.weixin.qq.com/s/E9ykuIbc-2Ja9HOY0bn_6g](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FE9ykuIbc-2Ja9HOY0bn_6g)
2.  B站公益74小时生信工程师教学视频合辑：[https://mp.weixin.qq.com/s/IyFK7l_WBAiUgqQi8O7Hxw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FIyFK7l_WBAiUgqQi8O7Hxw)
3.  招学徒：[https://mp.weixin.qq.com/s/KgbilzXnFjbKKunuw7NVfw](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FKgbilzXnFjbKKunuw7NVfw)

