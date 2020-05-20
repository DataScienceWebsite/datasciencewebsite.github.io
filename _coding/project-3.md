---
title: "Predicting Heart Disease Risk with Random Forest"
excerpt: "Identify the patients at risk of developing heart disease using R and random forest."
collection: coding
tags:
  - R
  - Bioinformatics
---

## Abstract
Heart disease is the leading cause of death in the United States ([1](https://www.cdc.gov/heartdisease/facts.htm)). To address this significant health issue, the Center for Disease Control (CDC) has a division dedicated to heart disease and stroke prevention ([2](https://www.cdc.gov/dhdsp/programs/spha/index.htm)). The CDC also recently started to use machine learning for prevention and diagnoses, which should be useful in identifying the population at risk ([3](https://www.electronicproducts.com/Programming/Software/The_CDC_uses_machine_learning_and_social_media_to_forecast_flu_outbreaks.aspx)).
Assisting diagnoses is an exciting and promising application of machine learning ([4](https://www.nature.com/articles/s41467-019-14225-8), [5](https://www.nature.com/articles/s41598-019-56889-8)), but the use of black box models is a potential issue ([6](https://www.thelancet.com/journals/lanres/article/PIIS2213-2600(18)304259/fulltext)). In this analysis, I used a decision tree open model to identify subjects with heart disease. The model achieved 80% accuracy, highlighting the potential of machine leering in assisting and accelerating the diagnosis of heart disease.

## Method

1. Initial Exploratory Data Analysis was performed with the DataExplorer package.
2. Data cleaning was performed with the Dplyr package.
3. Data visualization was performed with the ggplot2, ggpubr and the gridExtra packages.
4. Correlation study was performed with the GGally package.
6. Decision tree classification was performed with the caTools, rpart and rattle packages.
7. Random forest classification was performed with the randomForest package.

## Results
The notebook analysis can be seen <a href="https://www.kaggle.com/wguesdon/predicting-heart-disease-risk-with-random-forest" target="_blank">here</a>  

## Conclusion
Patient classification based on measured biological variables is a promising application of machine learning. It is also one area where the capacity for explaining the model’s result is equally important, and the usage of decision trees is one possible method to achieve an open box and accurate model.
Due to their limitations, the decision trees can't be used on all diagnosis problems. For example, the interpretation of images based on biological tests is one area where this model cannot be used efficiently and will require a black box neural network model. Ongoing research is focussing on this issue with the development of self-explanatory neural networks, which could help combine the modelling power of a neural network in an open model ([7](https://arxiv.org/pdf/1806.07538.pdf)).

## References

1. [cdc: Heart Disease Facts](https://www.cdc.gov/heartdisease/facts.htm) 
2. [cdc: Heart Disease and Stroke Programs](https://www.cdc.gov/dhdsp/programs/spha/index.htm)  
3. [cdc: machine learning](https://www.electronicproducts.com/Programming/Software/The_CDC_uses_machine_learning_and_social_media_to_forecast_flu_outbreaks.aspx)  
4. [ML: predict CLL risks](https://www.nature.com/articles/s41467-019-14225-8)  
5. [ML: heart failure prediction](https://www.nature.com/articles/s41598-019-56889-8)  
6. [Open ML model](https://www.thelancet.com/journals/lanres/article/PIIS2213-2600(18)304259/fulltext)  
7. [Self-Explaining Neural Networks](https://arxiv.org/pdf/1806.07538.pdf)  