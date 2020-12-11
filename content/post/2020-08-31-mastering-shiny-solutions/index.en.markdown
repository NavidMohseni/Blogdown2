---
title: Mastering Shiny Solutions (Chapter 2)
author: Navid Mohseni
date: '2020-06-20'
slug: mastering-shiny-solutions
categories:
  - R
tags:
  - Shiny
  - R
  - Mastering Shiny Solutions
subtitle: 'Chapter 2'
summary: ''
authors: []
lastmod: '2020-06-20T20:53:07+03:30'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

# Shiny Solutions


Chapter by chapter, I try to solve the exercises from the latest book of Hadley Wickham: [**Mastering Shiny**](https://mastering-shiny.org/). It is a step by step progress, and your suggestions are very welcome.


## Chapter 2



Load Shiny Package

```r
library(shiny)
```


Exercise 1

```r

#2.8.1
ui <- fluidPage(textInput("name", label = "What's your name?"),
                textOutput("greeting")
)

server <- function(input, output){
  output$greeting <- renderText({
  paste0("Hello ", input$name)
  })
}
shinyApp(ui, server)

```

Exercise 2

```r
#2.8.2
ui <- fluidPage(
  sliderInput("x", label = "If x is", min = 1, max = 50, value = 30),
  "then x times 5 is",
  textOutput("product")
)

server <- function(input, output, session) {
  output$product <- renderText({ 
    input$x * 5
  })
}
shinyApp(ui, server)
```

Exercise 3

```r
#2.8.3
ui <- fluidPage(
  sliderInput("x", label = "If x is", min = 1, max = 50, value = 30),
  sliderInput("y", label = "If y is", min = 1, max = 50, value = 30),
  "then, x multipled by y is",
  textOutput("product")
)


server <- function(input, output, session){
  output$product <- renderText({
    input$x * input$y
  })
}
shinyApp(ui, server)
```

Exercise 4

```r
#2.8.4
ui <- fluidPage(
  sliderInput("x", "If x is", min = 1, max = 50, value = 30),
  sliderInput("y", "and y is", min = 1, max = 50, value = 5),
  "then, (x * y) is", textOutput("product"),
  "and, (x * y) + 5 is", textOutput("product_plus5"),
  "and (x * y) + 10 is", textOutput("product_plus10")
)

server <- function(input, output, session) {
  Multiple <- reactive({
    input$x * input$y
  })
  output$product <- renderText({ 
    Multiple()
  })
  output$product_plus5 <- renderText({ 
    Multiple() + 5
  })
  output$product_plus10 <- renderText({ 
    Multiple() + 10
  })
}
shinyApp(ui,server)

```

Exercise 5

```r
#2.8.5
library(ggplot2)
datasets <- data(package = "ggplot2")$results[c(2, 4, 10), "Item"]

ui <- fluidPage(
  selectInput("dataset", "Dataset", choices = datasets),
  verbatimTextOutput("summary"),
  plotOutput("plot")
)

server <- function(input, output, session) {
  dataset <- reactive({
    get(input$dataset, "package:ggplot2")
  })
  output$summmry <- renderPrint({
    summary(dataset())
  })
  output$plot <- renderPlot({
    plot(dataset())
  }, res = 96)
}
shinyApp(ui, server)

```

 
 
