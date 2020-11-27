Raw Regression
================

``` r
library(tidyverse)

knitr::opts_chunk$set(
  fig.width = 9,
  fig.asp = .6,
  out.width = "90%")
```

We planned to use linear regression model to analysis the association
between obesity rate and other variables like sex, grade level, economic
status, etc. The resulting model can give us a sense of which factor
influences obesity rate the most.

Basic variables that we are interested in:

**Sex** **Grade Level** **Economic Status** **Food Access Ability**

# Exploratory data analysis

## Sex

``` r
original = 
  tibble(
  read.csv("./dataset/Student_Weight_Status_Category_Reporting_Results__Beginning_2010.csv")
) %>%
  janitor::clean_names()

sex_df = 
  original %>% 
  mutate(
    percent_healthy_weight = percent_healthy_weight * 100
  ) %>% 
  select(county, region, year_reported, percent_overweight_or_obese, grade_level, sex) %>% 
  filter(sex == "MALE" | sex == "FEMALE") %>%
  filter(grade_level == "DISTRICT TOTAL") %>% 
  filter(county == "STATEWIDE (EXCLUDING NYC)") %>% 
  select(year_reported, percent_overweight_or_obese, sex)

sex_count = 
  sex_df %>% 
  group_by(year_reported) 

sex_count %>% 
  ggplot(aes(x = as.factor(year_reported), y = percent_overweight_or_obese, fill = sex)) +
  geom_bar(stat='identity', position = "dodge") +
  labs(title = "Gender distribution in the database",
       x = "Year Reported",
       y = "percentage of Overweight or Obese") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

<img src="raw_regression_files/figure-gfm/unnamed-chunk-2-1.png" width="90%" />
