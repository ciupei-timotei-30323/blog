---
title: "Modeling Thermal Comfort"
date: 2026-02-23T11:51:05+02:00
summary: Creating a HVAC model using RFE 
tags: ["modeling", "projects", "python"]
draft: false
---

# Overview

- This project tried to model
the thermal comfort in
indoors enviroments.

- The prediction is made in
terms of 3 categories:
    - warmer, 
    - colder, 
    - no change ,


representing the action an
HVAC system might make.

- The data required for
modeling was sourced
from the ASHRAE
Thermal Comfort
Database II [^1].

- The dataset contained
subjective data as well
as instrumental data
and some thermal
indices.

# Data Preprocessing

- The first preprocessing measures I took were the following:
    - Removed non-numeric noise data (like ‘Publication’, ‘City’, etc.)
and removed °F measurements.
    - Replaced all missing data points with the median.
    - Removed features like ‘Age’, ‘Height’, etc.
    - Encoded all categorical data



In order to get even more useful information out of the data
set I derived some features using the original data.
For example `temp_diff` is computed using the difference
of the columns `Indoor` and `Outdoor Temperature`.

Also, Synthetic Minority Over-sampling Technique (**SMOTE**)
was used in order to enrich the training data with artificially
generated data points, while also combating imbalance in
the dataset.

![Graph showcasing the effect of smote](/images/SMOTE.png)


# Feature selection

For feature selection i used a **Random Forest Clasifier**

After a few iterations of trying a different numbers of
features, I observed that for this model, the more features,
the better the results.

The features were selected using a Random Forest and
then the features that contributed to the cumulative
importance up to a certain threshold were selected.

In the end, I selected for my model 18 features.

- Some of them are :
    - The interaction between Temperature & Clo [^2].
    - Air temperature in the occupied zone.
    - Predicted Mean Vote (PMV)
    - The temperature difference between inside & outside.

![Top 10 features selected](/images/features.png)

# Predictive Modeling

Here aswell, I used a **Random Forest Classifier** because its ability to handle non-linear
relationships while also being robust against outliers.

The data was split 80/20 before SMOTE was applied.
Hyperparameter tuning was used in order to find the best
tree depth and leaf size


The optimization objective was to maximize **Recall** for
discomfort classes.
The trade off was in a slightly lower accuracy.
The logic for choosing this was to steer the model in such a
way that it could in theory control a HVAC system.


The final model configuration has 500 trees,
with a minimum sample leaf of 4 and a
minimum sample split of 10.

# Model Evaluation

The evaluation was done using a confusion matrix and a precision recall curve aswell as computing the precision,recall and f1-score

![Confusion Matrix](/images/confusion.png)


![Precision-Recall graph](/images/PRC.png)

Classification Report:

| Category | Precision | Recall | F1-score |
| -------- | --------- | ------ | -------- |
| Cooler   | 0.61      | 0.6    | 0.6      |
| No change| 0.64      | 0.65   | 0.65     |
| Warmer   | 0.45      | 0.45   | 0.45     |

- Accuracy was **60%**

The model was tuned for Safety (detecting discomfort)
rather than pure Precision.
Trade-off: We accept a slightly higher false alarm rate
(predicting "Warmer" when a person is Neutral) to
ensure we don't freeze occupants.

- The code can be found on my [github](https://github.com/ciupei-timotei-30323/HVAC-model.git)


[^2]: Intrinsic Clothing Insulation
[^1]:https://www.sciencedirect.com/science/article/abs/pii/S0360132318303652

---
*T.*