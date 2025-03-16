
---
layout: single
classes: wide datavis-page
title: "数据可视化项目"
permalink: /cn/datavis1/
sidebar:
  nav: "cn-projects"
read_time: false
author_profile: false
header:
  overlay_image: "/assets/images/plot6.png"
  overlay_filter: 0.5
  caption: "犯罪数量与收入的关系图"
excerpt: "探索能影响犯罪数量的因素"
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

For complete data cleaning and transformation process: [view the code on github](https://github.com/donghao-wu/Final_Project)

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
    <a href = "/assets/quarter_plot.html" target = "_blank">
      <img src="/assets/images/quarter_plot.png" alt="Bar Chart">
    </a>
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
    <a href = "/assets/age_hist.html" target = "_blank">
      <img src="/assets/images/age_hist.png" alt="Scatter Plot">
    </a>
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

```
incident_median <- median(ncvs_p1$total_incident, na.rm = TRUE)
ncvs_p1 <- ncvs_p1 %>%
  mutate(Risk_Group = ifelse(total_incident > incident_median, "High-Risk Group", "Low-Risk Group"))
```

<div class="result-container reverse">
  <div class="result-text">
    <p>Diving a little bit deeper, I calculate the median of the total crime incident and classify the <strong>high-risk group as the total
    incidents is higher than the median</strong> and <strong>lower-risk group as total incidents is lower than the median.</strong> I used a scatter plot and 
    highlight the high-risk group. Using a scatter plot, audience can see more detail of each risk group. From the plot, we can see that the <strong>high
    risk group contains mostly age group ranging from 20 to around 65 </strong> with some exceptions such as age 43 and age 54</p>
  </div>
  <div class="result-image">
    <a href = "/assets/scatterplot_age.html" target = "_blank">
      <img src="/assets/images/scatterplot_age.png" alt="Bar Chart">
    </a>
  </div>
</div>

### Regional Variation of Crime Incidents in the U.S.
```
ncvs_data <- ncvs_data %>%
  mutate(Region_Label = recode(as.character(REGION),
                               "1" = "Northeast",
                               "2" = "Midwest",
                               "3" = "South",
                               "4" = "West"))
```
If you want to know which states are included in each label, please check the detailed code [here](https://github.com/donghao-wu/Final_Project)

<div class="result-container reverse">
  <div class="result-text">
    <p>The third relationship that I explored is the variation of crime incidents across the U.S.
    The regions in the data was separated into four different labels: Northeast, Midwest, South, and West
    . The data shows that the <strong>West region has the highest number of crime incidents</strong> while the <strong>Northeast region has the 
    the lowest number of crime incidents</strong>. Using a map plot is always helpful to provide a geographical sense to the audiences
    instead of purely numbers.</p>
  </div>
  <div class="result-image">
    <a href = "/assets/images/crime_rate_map.html" target = "_blank">
      <img src="/assets/images/crime_rate_map.png" alt="Bar Chart">
    </a>
  </div>
</div>


<div class="result-container">
  <div class="result-image">
    <a href = "/assets/region_barplot.html" target = "_blank">
      <img src="/assets/images/region_barplot.png" alt="Scatter Plot">
    </a>
  </div>
  <div class="result-text">
    <p>It also could not go wrong to use a histogram to provide more details on the data. With the histogram, audiences can have a 
    better comparison across each region and the histogram allow the audience to explore the actual numeric differences of the number
    of crime incidents across each region instead of a relatively ambiguous comparison.</p>
  </div>
</div>

### Relationship between crime incidents and income levels

```
mutate(Income_Label = recode(INCOME, 
                               "1" = "<$5K", "2" = "$5K-$7.5K", "3" = "$7.5K-$10K",
                               "4" = "$10K-$12.5K", "5" = "$12.5K-$15K", "6" = "$15K-$17.5K",
                               "7" = "$17.5K-$20K", "8" = "$20K-$25K", "9" = "$25K-$30K",
                               "10" = "$30K-$35K", "11" = "$35K-$40K", "12" = "$40K-$50K",
                               "13" = "$50K-$75K", "15" = "$75K-$100K", "16" = "$100K-$150K",
                               "17" = "$150K-$200K", "18" = ">$200K"))
```

<div class="result-container reverse">
  <div class="result-text">
    <p>The final exploration in the project examines the relationship between the number of incidents and income levels. The plot on the 
    right indicates that the likelihood of being involved in crime incidents is <strong>highest for the households with an income between $50K and $75K</strong>, 
    while it is <strong>lowest for those earning between $5K and $7.5K.</strong> </p>
  </div>
  <div class="result-image">
    <a href = "/assets/heatmap_income.html" target = "_blank">
      <img src="/assets/images/heatmap_income.png" alt="Bar Chart">
    </a>
  </div>
</div>



<div class="result-container">
  <div class="result-image">
    <a href = "/assets/images/incomebar.html" target = "_blank">
      <img src="/assets/images/incomebar.png" alt="Income Bar">
    </a>
  </div>
  <div class="result-text">
    <p>Alternatively, the distribution can also be displayed by bar plot. If the variation on the heatmap is not easy to visualize for some groups of audience,
    a bar plot like this one can be a little more helpful. Beyond which income level is associated with the highest probability of getting involved in crime incidents
    , audiences can make more detailed comparison (e.g. the number of incidents happened to income level 8 is around the half of income level 13)</p>
  </div>
</div>

## 🌟 Discussion:Insights from Crime Data Visualization

The analysis conducted in this project provides valuable insights into crime patterns based on seasonality, age, geographic distribution, and economic factors. 
By leveraging visual tools such as heatmaps, scatter plots, and histograms, key crime trends become more interpretable, supporting evidence-based policymaking 
and crime prevention strategies.

### Seasonal Trends and Unexpected Findings

One of the most counterintuitive findings in this study relates to the seasonality of crime incidents. Previous literature (McCord & Ratcliffe, 2018) suggests that crime, 
particularly aggressive offenses, tends to rise in warmer months due to increased human interaction and heightened aggression in higher temperatures. However, the quarterly 
crime analysis suggests that crime incidents are actually higher in Q1 and Q4, corresponding to autumn and winter.

- <strong>Potential Explanations:</strong>
  - <strong>Reporting Bias</strong>: There may be an increased tendency to report crimes in colder months due to heightened awareness or fewer distractions (e.g., summer vacations)
  - <strong>Types of Crime Matter</strong>: While violent crime might increase in summer, other types of crime (e.g., property crime, fraud) may occur more frequently in colder seasons.
  - <strong>Social and Economic Factors</strong>: Winter months could correlate with economic hardship, potentially increasing incidents of theft or domestic crimes.

This discrepancy suggests a need for further investigation into seasonal crime variations by specific crime types rather than aggregating all offenses into a single trend.

### Age and Crime Risk: Identifying High-Risk Groups

Crime victimization by age follows a predictable yet striking trend. As observed in the age distribution histogram, crime incidents tend to <strong>increase after age 25, peak between ages 30-50, 
and gradually decline after age 60.</strong>

- This aligns with <strong> criminological theories </strong> that suggest that <strong>individuals in their 30s and 40s, often more active in the workforce and social environments, might be at a greater risk of both offending and victimization.</strong>
- Interestingly, <strong>certain age groups (e.g., 43, 54) stand out as anomalies,</strong> indicating that additional socio-economic or occupational factors may contribute to heightened risk.

<strong> Policy Implications: </strong>

- Crime prevention initiatives should focus on individuals in their working-age years, particularly in areas with high exposure to social interaction (e.g., public transportation, nightlife areas).
- Programs targeting crime awareness and self-defense measures for middle-aged adults could be beneficial.

### Geographic Crime Patterns: Regional Disparities

Crime does not affect all regions equally, and as the U.S. regional analysis indicates, the West experiences the highest crime rate, while the Northeast has the lowest.

- <strong>Possible Reasons for Regional Differences:</strong>
  - <strong>Urbanization Levels</strong>: The West region contains high-density cities (e.g., Los Angeles, San Francisco, Seattle), where crime rates tend to be higher due to population density and economic disparity
  - <strong>Law Enforcement Differences</strong>: States within the Northeast may have stricter law enforcement policies or lower crime reporting
  - <strong>Cultural and Economic Variations</strong>: Differences in poverty rates, gun laws, and economic opportunities across regions could contribute to variations in crime rates

<strong> Policy Recommendations: </strong>
- <strong>For high-crime regions</strong>: Strengthen community policing efforts, improve economic development programs, and enhance public surveillance systems in urban areas
- <strong>For low-crime regions</strong>: Investigate whether low crime rates are due to genuine safety or under-reporting

### The Role of Income in Crime Victimization

One of the most critical takeaways from this study is the relationship between crime and income level.

- As the heatmap suggests, crime incidents are highest among households earning between $50K-$75K, not the lowest-income groups.
- This contradicts the common perception that the lowest-income groups are at the greatest risk.
  - <strong> Possible Explanations: </strong>
    - Middle-class individuals may have higher exposure to environments where crime occurs (e.g., commuting in public spaces, working in offices, shopping in commercial areas).
    - Certain lower-income groups may have lower reporting rates due to distrust in law enforcement.

## 📂 Additional Resources

- [View Codes & Data on Github](https://github.com/donghao-wu/Final_Project)
- [Shiny Interactive Application](https://luciuswu.shinyapps.io/Final_Project/)

