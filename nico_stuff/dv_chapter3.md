---
title: "Soc722 - DV Chapters 1-3"
author: "Turgut Keskintürk"
date: '2022-09-13'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---



Hello there! Let's start with our exercises for Kieran Healy's Data Visualization, Chapter 1 to 3.

# Set-Up


```r
# load the best package in the world
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
## ✔ readr   2.1.2      ✔ forcats 0.5.2 
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
# load the data
exercise_data <- read_csv("./Data/visualize_data.csv")
```

```
## New names:
## Rows: 142 Columns: 4
## ── Column specification
## ──────────────────────────────────────────────────────── Delimiter: "," dbl
## (4): ...1, ...2, Exercise, BMI
## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
## Specify the column types or set `show_col_types = FALSE` to quiet this message.
## • `` -> `...1`
## • `...1` -> `...2`
```

```r
glimpse(exercise_data)
```

```
## Rows: 142
## Columns: 4
## $ ...1     <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18…
## $ ...2     <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18…
## $ Exercise <dbl> 55.3846, 51.5385, 46.1538, 42.8205, 40.7692, 38.7179, 35.6410…
## $ BMI      <dbl> 1.8320590, 1.7892194, 1.7321050, 1.6178724, 1.5036362, 1.3751…
```

# Responses to Questions

## Question 1

I believe we will see a somewhat curvilinear relationship: people who record more exercise will have less BMI, but up to a point (simply because low BMI is also a sign of problem in terms of health).  


```r
cor(exercise_data$Exercise, exercise_data$BMI)
```

```
## [1] -0.06447185
```

Huh! No relation whatsoever. We have a slight negative correlation, but it is nearly non-existent.

Let's look at the data visually.


```r
exercise_data %>%
  ggplot(aes(Exercise, BMI)) +
  geom_point() +
  theme_bw() +
  labs(title = "The Relationship Between Exercise and BMI", 
       x = "Exercise (in Minutes)", y = "BMI")
```

![](dv_chapter3_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

Hahaha! Nice. This is awesome.

## Question 2

OK, we are now going to study with a dataset from ``causact`` package. Let's install it.


```r
install.packages("causact")
```

A couple of other set-ups.


```r
library(causact)
data(corruptDF)
glimpse(corruptDF)
```

```
## Rows: 174
## Columns: 7
## $ country     <chr> "Afghanistan", "Albania", "Algeria", "Angola", "Argentina"…
## $ region      <chr> "Asia Pacific", "East EU Cemt Asia", "MENA", "SSA", "Ameri…
## $ countryCode <chr> "AFG", "ALB", "DZA", "AGO", "ARG", "ARM", "AUS", "AUT", "A…
## $ regionCode  <chr> "AP", "ECA", "MENA", "SSA", "AME", "ECA", "AP", "WE/EU", "…
## $ population  <int> 35530081, 2873457, 41318142, 29784193, 44271041, 2930450, …
## $ CPI2017     <int> 15, 38, 33, 19, 39, 35, 77, 75, 31, 65, 36, 28, 68, 44, 75…
## $ HDI2017     <dbl> 0.498, 0.785, 0.754, 0.581, 0.825, 0.755, 0.939, 0.908, 0.…
```

So, ``CPI2017`` basically measures the perceived corruption in a country's public sector, while ``HDI2017`` mainly measures the degree to which a country is "developed," an average index that uses longevity, income and education.

## Question 3

Let's make a scatterplot.


```r
corruptDF %>%
  ggplot(aes(HDI2017, CPI2017)) +
  geom_point() +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption", 
       x = "Corruption Perceptions Index", y = "Human Development Index")
```

![](dv_chapter3_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

Nice. We see that there is a positive relationship between these variables, where low corruption is correlated with high development.

## Question 4

OK, let's add a new layer.


```r
# method 1

corruptDF %>%
  ggplot(aes(HDI2017, CPI2017)) +
  geom_point() +
  geom_smooth(method = "lm") +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption", 
       x = "Corruption Perceptions Index", y = "Human Development Index")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

![](dv_chapter3_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
# method 2

corruptDF %>%
  ggplot(aes(HDI2017, CPI2017)) +
  geom_point() +
  geom_smooth(method = "gam") +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption", 
       x = "Corruption Perceptions Index", y = "Human Development Index")
```

```
## `geom_smooth()` using formula 'y ~ s(x, bs = "cs")'
```

![](dv_chapter3_files/figure-html/unnamed-chunk-7-2.png)<!-- -->

The second method definitely fits the data better, as opposed to the linear method, which forces a linear structure to the relationship. Instead, the second method show that there is nearly a threshold value of corruption in terms of its relation to development.

## Question 5

OK, we are going to add another layer.


```r
corruptDF %>%
  ggplot(aes(HDI2017, CPI2017, fill = region, color = region)) +
  geom_point() +
  geom_smooth(method = "gam") +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption", 
       x = "Corruption Perceptions Index", y = "Human Development Index") +
  theme(legend.title = element_blank())
```

```
## `geom_smooth()` using formula 'y ~ s(x, bs = "cs")'
```

![](dv_chapter3_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Ugh! No way. Let's facet this graph to have a decent look.


```r
corruptDF %>%
  ggplot(aes(HDI2017, CPI2017)) +
  geom_point() +
  geom_smooth(method = "gam") +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption", 
       x = "Corruption Perceptions Index", y = "Human Development Index") +
  facet_wrap(~ region, nrow = 2)
```

```
## `geom_smooth()` using formula 'y ~ s(x, bs = "cs")'
```

![](dv_chapter3_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

## Question 6

We are now going to reverse the scale of the X axis.


```r
corruptDF %>%
  ggplot(aes(HDI2017, CPI2017)) +
  geom_point() +
  geom_smooth(method = "gam") +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption", 
       x = "Corruption Perceptions Index", y = "Human Development Index") +
  facet_wrap(~ region, nrow = 2) +
  scale_x_reverse()
```

```
## `geom_smooth()` using formula 'y ~ s(x, bs = "cs")'
```

![](dv_chapter3_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

Well, to be honest, this is a lot more intuitive simply because of the name of the measure!

## Question 7

Let's add a subtitle and caption.


```r
plot_corruptDF <- corruptDF %>%
  ggplot(aes(HDI2017, CPI2017)) +
  geom_point() +
  geom_smooth(method = "gam") +
  theme_bw() +
  labs(title = "The Relationship Between Human Development and Perceived Corruption",
       subtitle = "Observations As Countries. N = 174.",
       x = "Corruption Perceptions Index", y = "Human Development Index",
       caption = "Corruption Index comes from Transparancy International, while Development Index comes from UNDP.") +
  facet_wrap(~ region, nrow = 2) +
  scale_x_reverse()

plot_corruptDF
```

```
## `geom_smooth()` using formula 'y ~ s(x, bs = "cs")'
```

![](dv_chapter3_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

## Question 8

This is nice! Let's save the plot to as PNG file.


```r
ggsave("cpi_vs_hdi_plot.png", plot = plot_corruptDF, h = 7.5, w = 10, units = "in")
```

```
## `geom_smooth()` using formula 'y ~ s(x, bs = "cs")'
```

Thank you!
