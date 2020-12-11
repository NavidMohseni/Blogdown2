---
title: Machine Learning with R, tidyverse, mlr (Chapter 4)
author: Navid Mohseni
date: '2020-10-04'
slug: machine-learning-with-r-tidyverse-mlr
categories:
  - R
  - Machine Learning
tags:
  - Machine Learning
  - tidyverse
  - R
subtitle: 'Chapter 4: Logistic Regression'
summary: ''
authors: []
lastmod: '2020-12-11T14:18:24+03:30'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

# Machine Learning with R, tidyverse Solutions

Chapter by chapter, I try to solve the exercises of the Hefin loan Rhys book: [**Machine Learning with R, the tidyverse, and mlr**](https://www.manning.com/books/machine-learning-with-r-the-tidyverse-and-mlr). It is a step by step progress, and your suggestions are very welcome.
 

## Chapter 4: Logistic Regression





```r
#Logistic Regression
library(mlr)
library(tidyverse)

data(titanic_train, package = "titanic") %>% as_tibble()
titanicTib <- as_tibble(titanic_train)
fctrs <- c("Survived", "Sex", "Pclass")
titanicClean <- titanicTib %>% 
  mutate_at(.vars = fctrs, .funs = factor) %>% 
  mutate(FamSize = SibSp + Parch) %>% 
  select(Survived, Pclass, Sex, Age, Fare, FamSize)

titanicUntidy <- gather(titanicClean, key = "Variable", value = "Value", -Survived)
titanicUntidy %>% filter(Variable != "Pclass" & Variable != "Sex") %>% 
  ggplot(aes(x = Survived, y = as.numeric(Value))) + 
  facet_wrap(~Variable, scales = "free_y") + 
  geom_violin(draw_quantiles = c(0.25, 0.5, 0.75)) + 
  theme_bw()

titanicUntidy %>% filter(Variable != "Pclass" & Variable != "Sex") %>% 
  ggplot(aes(x = Survived, y = as.numeric(Value))) + facet_wrap(~Variable, scales = "free_y") + 
  geom_point(size = 3, alpha = 0.05) + theme_bw()

titanicUntidy %>% filter(Variable == "Sex" | Variable == "Pclass") %>% 
  ggplot(aes(Value, fill = Survived)) + 
  facet_wrap(~Variable, scales = "free_x") + 
  geom_bar(position = "fill") + 
  theme_bw()

titanicUntidy %>% filter(Variable == "Sex" | Variable == "Pclass") %>% 
  ggplot(aes(Value, fill = Survived)) + 
  facet_wrap(~Variable, scales = "free_x") + 
  geom_bar(position = "dodge") + 
  theme_bw()

titanicUntidy %>% filter(Variable == "Sex" | Variable == "Pclass") %>% 
  ggplot(aes(Value, fill = Survived)) + 
  facet_wrap(~Variable, scales = "free_x") + 
  geom_bar(position = "stack") + 
  theme_bw()

imp <- impute(titanicClean, cols = list(Age = imputeMean()))
imp

titanic_task <- makeClassifTask(data = imp$data, target = "Survived")
logreg <- makeLearner("classif.logreg", predict.type = "prob")
logregModel <- train(logreg, titanic_task)

logregwrapper <- makeImputeWrapper("classif.logreg", 
                                   cols = list(Age = imputeMean()))
kfold <- makeResampleDesc(method = "RepCV", folds = 10, reps = 50, stratify = TRUE)
logregwithImpute <- resample(task = titanic_task, learner = logregwrapper,
                             resampling = kfold, measures = list(acc, fpr, fnr))

logregModeldata <- getLearnerModel(logregModel)
logregModeldata %>% coef()
exp(cbind(Odds_ratio = coef(logregModeldata), confint(logregModeldata)))

#Exercise1
titanicClean2 <- titanicClean %>% select(-Fare)
imp2 <- impute(titanicClean2, cols = list(Age = imputeMean()))    

titanic_task2 <- makeClassifTask(data = imp2$data, target = "Survived")
logregwrapper2 <- makeImputeWrapper("classif.logreg", 
                                    cols = list(Age = imputeMean()))
logregwithImpute <- resample(task = titanic_task2, 
                             learner = logregwrapper2,
                             resampling = kfold,
                             measures = list(acc, fpr, fnr))

logregModel2 <- train(logreg, titanic_task2)
logregModeldata2 <- getLearnerModel(logregModel2)
logregModeldata2 %>% coef()
exp(cbind(Odds_ratio = coef(logregModeldata2), confint(logregModeldata2)))

titanicTib
titanicTib %>% str_split("Name", pattern = "\\.")
titanicTib$Name %>% str_split(pattern = "\\.")

```

