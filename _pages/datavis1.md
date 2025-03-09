---
layout: single
title: "Final Project of Data Visualization"
permalink: /projects/datavis1/
sidebar:
  nav: "projects"
read_time: false
author_profile: false
header:
  overlay_image: "/assets/images/datavis_cover.png"
  overlay_filter: 0.5
  caption: "Credit: Unsplash"
excerpt: "Exploring insights through stunning data visualization"
toc: true  
toc_label: "Project Overview"
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
that higher levels of inequality, unemployment, and poverty are strongly associated with increased 
crime rates (de Nadai et al., 2020). Beyond socio-economic factors, research has also highlighted 
the impact of environmental conditions, such as temperature. McCord and Ratcliffe (2018) found 
that higher temperatures contribute to an increase in aggressive crimes. Additionally, studies 
suggest that higher rates of gun ownership are linked to increased violent crime (Bhattacharya, 2020). 
Given the multitude of potential factors influencing crime, this project will utilize data from 
the National Crime Victimization Survey (NCVS) and machine learning techniques to explore key 
determinants of total crime incidents.

## ❓Research Question

**What are some influential factors of total crime incidents?**

## 🔢 Introduction to the Data set

**Data Source**

The National Crime Victimization Survey(NCVS) Data is downloaded from :https://www.icpsr.umich.edu/web/NACJD/studies/38090/summary 
and will be saved as a csv file named ncvs in this project. The NCVS was designed with four primary objectives: (1) to develop detailed 
information about the victims and consequences of crime, (2) to estimate the number and types of crimes not reported to the police, (3) 
to provide uniform measures of selected types of crimes, and (4) to permit comparisons over time and types of areas. Original data set contains 
**8043 observations**, each observation is a victim from crime and **81 variables**. (Not all the variables will be included in this project)

## 🛠️Methods

This project employs various data visualization techniques to make crime trends interpretable and actionable. Key methodologies include:

**Data Cleaning & Transformation**

- The dataset is filtered to remove invalid or placeholder values (e.g., age values of 98/99 and income levels coded as missing).
- The YEARQ variable is separated into YEAR and QUARTER to enable temporal analysis.
- The REGION variable is recoded for better interpretability (e.g., 1 = Northeast, 2 = Midwest, etc.).

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

## Results

{% include gallery id="gallery" %}


[View Code on GitHub](https://github.com/yourrepo)
