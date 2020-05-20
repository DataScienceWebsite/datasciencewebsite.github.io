---
title: "Stroke prevention: clustering and risks factors identification"
excerpt: "Identify the risk factors associated with stroke incidence using R."
collection: coding
tags:
  - R
  - Bioinformatics
---

## Abstract
The goal of this project is to use statistical learning to identify the combination of the features that are more likely to be associated with stroke. For this analysis, I first performed an exploratory data analysis and feature engineering. As often seen in health-related datasets, the proportion of patients with stroke is low, resulting in a severely unbalanced dataset. To compensate for the unbalance, I used the ROSE package to artificially over sample the number of strokes observations.
I then used a dimension reduction technique adapted to mixed datasets of continuous and categorical variables with the FAMD package. This visualization allowed me to identify the features most likely associated with the risk of developing a stroke. Using these selected variables, I built a logistic regression model to evaluate the contribution of each feature to the risk of developing a stroke. The logistic regression model identified the Age, Average Glucose Level, Smoking, Hypertension, and Heart Diseases features as the most likely risk factors of developing a stroke. Age was the principal risk factor in this study.

The notebook analysis can be seen <a href="https://www.kaggle.com/wguesdon/stroke-prevention-clustering-and-risks-factors" target="_blank">here</a>  