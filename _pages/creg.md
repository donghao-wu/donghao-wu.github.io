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

*If you are more interested in a slide version* [click here](https://gamma.app/docs/Crime-Data-Machine-Learning-clki9sxnuh0pkyd?mode=doc)
*For the complete code* [click here](https://github.com/donghao-wu/Crime_ML)

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



<div class="result-container">
  <div class="result-image">
    <img src="/assets/images/eda.png" alt="Scatter Plot">
  </div>
  <div class="result-text">
    <p>For the Explanatory Describtion Analysis (EDA), this project calculated the mean, standard deviation, 
    min, max, and other relevant statistics, which are shown on the image on the left</p>
  </div>
</div>