---
layout: single
classes: wide
title: "Final Project of Data Visualization"
permalink: /projects/datavis1/
sidebar:
  nav: "projects"
read_time: false
author_profile: false
header:
  overlay_image: "/assets/images/plot6.png"
  overlay_filter: 0.5
  caption: "Relationship between income level and crime incidents"
excerpt: "Exploring influential factors of crime incidents"
toc: true  
toc_label: "Project Overview"
toc_icon: "fa-solid fa-palette"
toc_sticky: true

gallery:
  - url: "/assets/images/plot1.png"
    image_path: "/assets/images/plot1.png"
    alt: "Histogram"
    title: "📉 Relationship between age and number of incidents"

  - url: "/assets/images/plot2.png"
    image_path: "/assets/images/plot2.png"
    alt: "Histogram"
    title: "🔥 Seasonal Crime Trends"

  - url: "/assets/images/plot3.png"
    image_path: "/assets/images/plot3.png"
    alt: "Histogram"
    title: "🔢 Crime Rates Across U.S. Regions"
    
  - url: "/assets/images/plot4.png"
    image_path: "/assets/images/plot4.png"
    alt: "Scatter Plot"
    title: "🔢 High-risk vs. Low-risk Groups"
    
  - url: "/assets/images/plot5.png"
    image_path: "/assets/images/plot5.png"
    alt: "Heatmap"
    title: "🔢 Heatmap analysis of Income Levels"
    
---

## 📊 Introduction to the Project

Crime has been a widespread and serious issue in society since the establishment of laws. 
Researchers have extensively investigated the factors influencing crime rates. Studies have shown 
that **higher levels of inequality, unemployment, and poverty** are strongly associated with increased 
crime rates (de Nadai et al., 2020). Beyond socio-economic factors, research has also highlighted 
the impact of environmental conditions, such as temperature. McCord and Ratcliffe (2018) found 
that **higher temperatures contribute to an increase in aggressive crimes**. Additionally, studies 
suggest that **higher rates of gun ownership are linked to increased violent crime** (Bhattacharya, 2020). 
Given the multitude of potential factors influencing crime, this project will utilize data from 
the National Crime Victimization Survey (NCVS) and machine learning techniques to explore key 
determinants of total crime incidents.

## ❓Research Question

**What are some influential factors of total crime incidents?**

## 🔢 Introduction to the Dataset

**Data Source**

The National Crime Victimization Survey(NCVS) Data is downloaded from [NACJD website](https://www.icpsr.umich.edu/web/NACJD/studies/38090/summary) and will be saved as a csv file named **ncvs** in this project. The NCVS was designed with **four primary objectives**: (1) to develop detailed 
information about the victims and consequences of crime, (2) to estimate the number and types of crimes not reported to the police, (3) 
to provide uniform measures of selected types of crimes, and (4) to permit comparisons over time and types of areas. Original dataset contains 
**8043 observations**, each observation is a victim from crime and **81 variables**. (Not all the variables will be included in this project)

```
ncvs_data <- read.csv("data/NCVS_2020.csv")
```

## 🛠️Methods

This project employs various data visualization techniques to make crime trends interpretable and actionable. Key methodologies include:

**Data Cleaning & Transformation**

- The dataset is filtered to remove invalid or placeholder values (e.g., age values of 98/99 and income levels coded as missing).
- The YEARQ variable is separated into YEAR and QUARTER to enable temporal analysis.
- The REGION variable is recoded for better interpretability (e.g., 1 = Northeast, 2 = Midwest, etc.).

```
ncvs_data <- ncvs_data %>%
  separate(YEARQ, into = c("YEAR", "QUARTER"), sep = "\\.", convert = TRUE) %>% 
  mutate(Region_Label = recode(REGION, `1` = "Northeast", `2` = "Midwest", `3` = "South", `4` = "West"))
```

For complete data cleaning and transformation process: [view the code on github](https://github.com/MACS40700/assignment-5-donghao-wu)

**Visualization Techniques**

- **Histogram Visualization**: Displays the distribution of crime incidents by selected demographic variables (e.g., age, income).
- **Bar Charts**: Highlights crime counts per region to illustrate geographical disparities.
- **Scatter Plots with Trend Lines**: Examines age-related crime patterns using LOESS smoothing to highlight trends in incidents by age group.
- **Heatmaps**: Depicts crime incidents by income level to illustrate economic disparities in victimization.
- **Quarterly Trend Analysis**: Uses bar charts to display seasonal crime variations.

**Interactivity**

- Dropdown menus allow users to select variables of interest for customized analysis.
- Checkboxes for time selection enable filtering crime trends by different quarters.
- Dynamic tooltips in visualizations provide contextual insights when hovering over data points.

## ✅Results

According to the results obtained from the plots, there are several important conclusions
can be extracted. In the following sections, I will use several plots to demonstrate some 
important relationships

### Crime trend across the quarters

```
colors <- c("#1abc9c", "#3498db", "#9b59b6", "#e74c3c")

gg <- ggplot(ncvs_data, aes(x = factor(QUARTER), y = NUM_INCIDENTS, fill = factor(QUARTER))) +
      geom_col(show.legend = FALSE) +
      scale_fill_manual(values = colors) +
      labs(title = "Seasonal Crime Trends",
          x = "Quarter",
          y = "Total Incidents") +
      theme_minimal()
```

<div class="result-container reverse">
  <div class="result-text">
    <p>The first relationship that I want to explore is the variation of crime incidents over the year.
    As mentioned in the introduction section, McCord and Ratcliffe (2018) found that 
    <strong>higher temperatures contribute to an increase in aggressive crimes</strong>. Therefore, it is reasonable 
    to assume that the total number of crime incidents should be <strong>higher around the summer(Q2 - Q3)</strong>
    However, the data is showing quite the opposite, the total number of crime incidents is actually <strong>higher in the Q1 and Q4</strong>
    , which correspond to <strong>Autumn and Winter</strong>. Even though this result is not representative enough, this could
    be an interesting contradiction to the previous findings. Using histogram can facilitate the comparison between the quarters
    and changing the color corresponding to the season will improve the aesthetics of the plot</p>
  </div>
  <div class="result-image">
    <img src="/assets/images/quarter_plot.png" alt="Bar Chart">
  </div>
</div>

### Relationship between the number of crime incident and age
```
#The code is used for Shiny App so it will be slightly different from directly plotting
gg <- ggplot(ncvs_data, aes_string(x = input$variable_interest)) +
    geom_histogram(fill = "#3498db", color = "white", bins = 30, alpha = 0.8) +
    labs(title = paste("Distribution of", input$variable_interest),
          x = input$variable_interest, y = "Count") +
    theme_minimal()
```
<div class="result-container">
  <div class="result-image">
    <img src="/assets/images/plot1.png" alt="Scatter Plot">
  </div>
  <div class="result-text">
    <p>The histogram showing on the left is showing how total number of crime incidents vary
    across different ages. As seen in the plot, the total number of crime incident is showing
    an <strong>increase after 25 years old</strong> and this trend will start to <strong>decrease after age 60</strong>. The 
    distribution shows that the probability of <strong>getting involved in a crime incident is higher 
    for middle-age group</strong>. Using histogram will allow the users to easily capture the distribution
    of the crime incidents among different ages and easily see the clusters in the data.</p>
  </div>
</div>



[View Code on GitHub](https://github.com/MACS40700/assignment-5-donghao-wu)
