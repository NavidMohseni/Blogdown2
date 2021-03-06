---
title: Machine Learning with R, tidyverse, and mlr Solutions (Chapter 6)
author: Navid Mohseni
date: '2020-10-08'
slug: machine-learning-with-r-tidyverse-mlr-solutions
categories:
  - R
  - Machine Learning
tags:
  - R
  - Machine Learning
  - Machine Learning with R solutions
  - tidyverse
  - mlr
  - book
subtitle: 'Chapter 6: Classifying with naive Bayes and support vector machines'
summary: ''
authors: []
lastmod: '2020-10-08T21:28:39+03:30'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---


# Machine Learning with R, tidyverse Solutions

Chapter by chapter, I try to solve the exercises of the Hefin loan Rhys book: [**Machine Learning with R, the tidyverse, and mlr**](https://www.manning.com/books/machine-learning-with-r-the-tidyverse-and-mlr). It is a step by step progress, and your suggestions are very welcome.
 

## Chapter 6




```r
library(mlr)
library(tidyverse)

data(HouseVotes84, package = "mlbench")
votesTib <- as_tibble(HouseVotes84)

map_dbl(votesTib, ~sum(is.na(.)))
map_dbl(votesTib, ~length(which(. == "y")))

votesUntidy <- gather(votesTib, "Variable", "Value", -Class)
votesUntidy %>% ggplot(aes(x = Class, fill = Value)) + facet_wrap(~Variable, scales = "free_y") + 
  geom_bar(position = "fill")

votesTask <- makeClassifTask(data = votesTib, target = "Class")
bayes <- makeLearner("classif.naiveBayes")
bayesModel <- train(learner = bayes, task = votesTask)

kfold <- makeResampleDesc("RepCV", folds = 10, reps = 50, stratify = TRUE)
bayesCV <- resample("classif.naiveBayes", task = votesTask, 
                    resampling = kfold,
                    measures = list(mmce, acc, fpr, fnr))

politician <- tibble(V1 = "n", V2 = "n", V3 = "y", V4 = "n", V5 = "n",
                     V6 = "y", V7 = "y", V8 = "y", V9 = "y", V10 = "y",
                     V11 = "n", V12 = "y", V13 = "n", V14 = "n",
                     V15 = "y", V16 = "n")
politicianPred <- predict(bayesModel, newdata = politician)
getPredictionResponse(politicianPred)


data(spam, package = "kernlab")
spamTib <- as_tibble(spam)
spamTib
spam.task
svm <- makeLearner("classif.svm")
getParamSet("classif.svm")
```

