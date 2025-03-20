---
layout: single
classes: wide datavis-page
title: "Crime Data and ML Regression Models"
permalink: /projects/creg/
sidebar:
  nav: "projects"
read_time: false
author_profile: false
header:
  overlay_image: "/assets/images/creg_cover.png"
  overlay_filter: 0.5
  caption: "Credit:Unsplash"
excerpt: "Exploring influential factors of crime incidents"
---

- *If you are more interested in a slide version* [click here](https://gamma.app/docs/Crime-Data-Machine-Learning-clki9sxnuh0pkyd?mode=doc)

- *For the complete code* [click here](https://github.com/donghao-wu/Crime_ML)

### Introduction to the Project

Crime has been a widespread and serious issue in society since the establishment of laws. Researchers have extensively investigated the factors influencing crime rates. 
Studies have shown that higher levels of inequality, unemployment, and poverty are strongly associated with increased crime rates (de Nadai et al., 2020). Beyond socio-economic factors, 
research has also highlighted the impact of environmental conditions, such as temperature. McCord and Ratcliffe (2018) found that higher temperatures contribute to an increase in aggressive crimes. 
Additionally, studies suggest that higher rates of gun ownership are linked to increased violent crime (Bhattacharya, 2020). Given the multitude of potential factors influencing crime, 
this project will utilize data from the National Crime Victimization Survey (NCVS) and machine learning techniques to explore key determinants of total crime incidents.

### Research Question

What are some influential factors of total crime incidents?

### Data Source

The National Crime Victimization Survey(NCVS) Data is downloaded from their [official website](https://www.icpsr.umich.edu/web/NACJD/studies/38090/summary)
and will be saved as a csv file named ncvs in this project. The NCVS was designed with four primary objectives: (1) to develop detailed information about the victims and consequences of crime, 
(2) to estimate the number and types of crimes not reported to the police, (3) to provide uniform measures of selected types of crimes, and (4) to permit comparisons over time and types of areas. 
Original data set contains **8043 observations**, each observation is a victim from crime and **81 variables**. (Not all the variables will be included in this project)

### Code Implementation

```
#import packages and data
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split

#load data
ncvs = pd.read_csv('NCVS_2020.csv')
```

**Feature Selection**
The features selected for this project will include `URBANICITY`, `GATED`, `MARITAL`, `AGE`, `SEX`, `RACE`,`ED`, `INCOME`, `REGION`, `WEAPON`. The target variable will be `NUM_INCIDENTS`

```
#select features and target
selected_features = ["URBANICITY", "GATED", "MARITAL", "AGE", "SEX", "RACE", "ED", "INCOME", "REGION", "WEAPON", "NUM_INCIDENTS"]
ncvs_new = ncvs[selected_features]

#split the features into categorical and numerical
unique_values = ncvs_new.nunique()
categorical_features = ["URBANICITY", "GATED", "MARITAL", "SEX", "RACE", "ED", "REGION", "WEAPON"]
numerical_features = ["AGE", "INCOME"]

```
**Explanatory Description Analysis**

Necessary EDA will be performed to get a general idea of the variables.

```
summary_statistics = ncvs[selected_features].describe(include="all")
```

<div class="result-container">
  <div class="result-image">
    <img src="/assets/images/eda.png" alt="Scatter Plot">
  </div>
  <div class="result-text">
    <p>For the Explanatory Describtion Analysis (EDA), this project calculated the mean, standard deviation, 
    min, max, and other relevant statistics, which are shown on the image on the left</p>
  </div>
</div>

**Data Manipulation and Cleaning**

Before the machine learning tasks, necessary data cleaning and data manipulations need to be done first. I will filter out the value that represents residue, invalid, or out of universe according to the codebook. 
Manipulate coding to make the results more interpretable

```
filter_conditions = {
    "URBANICITY": [1, 2, 3],
    "GATED": [1, 2],
    "MARITAL": [1, 2, 3, 4, 5],
    "SEX": [1, 2],
    "REGION": [1, 2, 3, 4],
    "WEAPON": [1, 2, 3]
}
for col, valid_values in filter_conditions.items():
    ncvs_new = ncvs_new[ncvs_new[col].isin(valid_values)]

education_mapping = {
    0: "Elementary", 1: "Elementary", 2: "Elementary", 3: "Elementary", 4: "Elementary",
    5: "Elementary", 6: "Elementary", 7: "Elementary", 8: "Elementary", 9: "High School",
    10: "High School", 11: "High School", 12: "High School", 21: "College", 22: "College",
    23: "College", 24: "College", 25: "College", 26: "College", 27: "No Diploma",
    28: "High School Grad", 40: "Some College", 41: "Associate Degree", 42: "Bachelor Degree",
    43: "Master Degree", 44: "Prof School Degree", 45: "Doctorate Degree", 98: "Residue",
    99: "Out of Universe"
}

# Apply the mapping
ncvs_new["ED"] = ncvs_new["ED"].map(education_mapping)

# Filter out Residue and Out of Universe values
ncvs_new = ncvs_new[~ncvs_new["ED"].isin(["Residue", "Out of Universe"])]
```

**Outlier Detections and Handle**

<div class="result-container">
  <div class="result-image">
    <img src="/assets/images/dist.png" alt="Scatter Plot">
  </div>
  <div class="result-text">
    <p>Graph shown on the left is showing the distribution of the target variable, number of incidents</p>
  </div>
</div>

After filtering out the unvalid data, the next step that I would do is to detect and deal with the outliers. The detection of outliers will be conducted using IQR calculations. The distribution of target variable is presented below

```
# Identify potential outliers in NUM_INCIDENTS using IQR method
Q1 = ncvs_new["NUM_INCIDENTS"].quantile(0.25)
Q3 = ncvs_new["NUM_INCIDENTS"].quantile(0.75)
IQR = Q3 - Q1

# Define outlier threshold
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

# Count outliers
outliers = ncvs_new[(ncvs_new["NUM_INCIDENTS"] < lower_bound) | (ncvs_new["NUM_INCIDENTS"] > upper_bound)]
num_outliers = outliers.shape[0]
print(f"Number of Outliers: {num_outliers}")
print(outliers.head())

# Handle outliers by capping NUM_INCIDENTS at the upper IQR bound
ncvs_new["NUM_INCIDENTS"] = ncvs_new["NUM_INCIDENTS"].apply(lambda x: min(x, upper_bound))

# Encode categorical features using one-hot encoding
ncvs_new_encoded = pd.get_dummies(ncvs_new, columns=categorical_features, drop_first=True)
```
Outlier detection shows that there are 562 outliers included in the dataset, in order to avoid reducing the sample size,
the outlier is handled using capping method so that the outliers will be changed to the upper bound of the IQR.

**Train-Test-Validation Split**

For this project, I will split the data into training data, test data, and validation data. The proportion for each data will be **70%** for training data, **15%** for testing data and validation data

```
# Split dataset into training (70%), validation (15%), and testing (15%)
train_ncvs, temp_ncvs = train_test_split(ncvs_new_encoded, test_size=0.3, random_state=42)
val_ncvs, test_ncvs = train_test_split(temp_ncvs, test_size=0.5, random_state=42)

# Display dataset sizes
dataset_sizes = {
    "Training Set": train_ncvs.shape,
    "Validation Set": val_ncvs.shape,
    "Test Set": test_ncvs.shape,
}
print(dataset_sizes)

#{'Training Set': (5577, 40), 'Validation Set': (1195, 40), 'Test Set': (1196, 40)}
```
### Model Selection and Fine Tuning