Final Project
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ───────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

# Clean the orginal weight dataset

``` r
original = tibble(
  read.csv("./dataset/Student_Weight_Status_Category_Reporting_Results__Beginning_2010.csv")
) %>%
  janitor::clean_names() %>%
  filter(year_reported %in% c("2010-2011", "2018-2019")) %>% #only need data in year 2010-2011 or 2018-2019
  select(-location_code, -region, -area_name)  # the only location information we need is county name
```

# Clean dataset with geolocation information

``` r
#import coordinates data set 
coordinates = tibble(
  read.csv("./dataset/Geocodes_USA_with_Counties.csv")
) %>%
  filter(state == "NY") %>%  # filter out counties outside NY state
  select(county, latitude, longitude) %>% # only information we need is county name and geolocation
  drop_na() %>%
  group_by(county) %>%
  summarise(latitude = mean(latitude), longitude = mean(longitude)) %>% #different location in each county variaed slightly, so we take the mean of each county's geolocation
  filter(!county == "") %>% # one county's name input is blank
  mutate(county = toupper(county)) # to swith county name to uppercase
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

# combine two data set

``` r
weight_df = left_join(original, coordinates, by = "county")

average_percent = 
  weight_df %>% 
  group_by(county) %>% 
  drop_na(percent_overweight_or_obese) %>%
  summarize(average = mean(percent_overweight_or_obese), .groups = "keep")

sum_df = left_join(weight_df, average_percent, by = "county")
```
