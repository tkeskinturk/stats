---
title: "Soc722 - MD Chapter 4"
author: "Turgut Keskintürk"
date: '2022-09-07'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---



Hello there! Let's start with our exercises for Chapter 4.

# Set-Up

First, we will begin with loading the tidyverse package (thanks to the beloved Hadley).


```r
# loading the tidyverse package
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
## ✔ ggplot2 3.3.6     ✔ purrr   0.3.4
## ✔ tibble  3.1.8     ✔ dplyr   1.0.9
## ✔ tidyr   1.2.0     ✔ stringr 1.4.1
## ✔ readr   2.1.2     ✔ forcats 0.5.2
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

# Responses to Questions

Nice, let's start.

## Question 1

Let's load the NFL salaries data through the Web.


```r
df <- read_csv(
  "https://raw.githubusercontent.com/NicolasRestrep/223_course/main/Data/nfl_salaries.csv"
)
```

```
## Rows: 800 Columns: 11
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## dbl (11): year, Cornerback, Defensive Lineman, Linebacker, Offensive Lineman...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
# never forget
glimpse(df)
```

```
## Rows: 800
## Columns: 11
## $ year                <dbl> 2011, 2011, 2011, 2011, 2011, 2011, 2011, 2011, 20…
## $ Cornerback          <dbl> 11265916, 11000000, 10000000, 10000000, 10000000, …
## $ `Defensive Lineman` <dbl> 17818000, 16200000, 12476000, 11904706, 11762782, …
## $ Linebacker          <dbl> 16420000, 15623000, 11825000, 10083333, 10020000, …
## $ `Offensive Lineman` <dbl> 15960000, 12800000, 11767500, 10358200, 10000000, …
## $ Quarterback         <dbl> 17228125, 16000000, 14400000, 14100000, 13510000, …
## $ `Running Back`      <dbl> 12955000, 10873833, 9479000, 7700000, 7500000, 703…
## $ Safety              <dbl> 8871428, 8787500, 8282500, 8000000, 7804333, 76527…
## $ `Special Teamer`    <dbl> 4300000, 3725000, 3556176, 3500000, 3250000, 32250…
## $ `Tight End`         <dbl> 8734375, 8591000, 8290000, 7723333, 6974666, 61333…
## $ `Wide Receiver`     <dbl> 16250000, 14175000, 11424000, 11415000, 10800000, …
```

We have the data! I am not sure if those millions will look nice on the graphs, but we'll think of something.

## Question 2

Let's turn this dataset into the tidy format.


```r
df <- df %>%
  pivot_longer(
    cols = -year, names_to = "position", values_to = "salary"
  )
```

## Question 3

OK, we now have the dataset with the tidy format. Let's look at the salaries of quarterbacks.


```r
# filter the dataset
df_quarters <- df %>%
  filter(position == "Quarterback")

# let's make a histogram for each year
df_quarters %>%
  drop_na() %>% # some NAs
  ggplot(aes(x = salary/1000000)) + # to avoid the ugly x-axis (though I kinda like the scientific notation)
  geom_histogram(fill = "steelblue", color = "white") +
  theme_bw() +
  labs(
    title = "Salary Distributions of Quarterbacks", 
    x = "Salaries (in Millions)", y = "# of Players") +
  facet_wrap(~ year, nrow = 2)
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](md_chapter4_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

We see a right-skewed distribution in all years, meaning that some players make a lot more than the average players in the dataset. In 2018, though, we have one salary that's astronomical even compared to the previous years.

## Question 4

Now, we are going to look at the average salaries for each year and position. Let's go.


```r
average_salaries <- df %>%
  group_by(year, position) %>%
  summarise(mean = mean(salary, na.rm = TRUE))
```

```
## `summarise()` has grouped output by 'year'. You can override using the
## `.groups` argument.
```

```r
# a glimpse to the dataset
average_salaries %>% head()
```

```
## # A tibble: 6 × 3
## # Groups:   year [1]
##    year position              mean
##   <dbl> <chr>                <dbl>
## 1  2011 Cornerback        3037766.
## 2  2011 Defensive Lineman 4306995.
## 3  2011 Linebacker        4016045.
## 4  2011 Offensive Lineman 4662748.
## 5  2011 Quarterback       3376113.
## 6  2011 Running Back      1976341.
```

## Question 5

Now, we are going to create a linegraph to trace the evolution of salaries for each position.

I am going to use two different graphs. First, I am going to construct a linegraph that distinguishes different positions through different lines. I am not a fan of these plots, though. Thus, secondly, I am going to create linegraphs faceted by position.


```r
average_salaries %>%
  ggplot(aes(x = year, y = (mean/1000000), group = position, col = position)) +
  geom_line() +
  theme_bw() +
  labs(
    title = "Change in Average Salaries in Positions",
    x = "Year", y = "Average Salaries (in Millions)")
```

![](md_chapter4_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

OK, our first trend is that there is a relative increase in the salaries of nearly all positions, but the rank ordering of the positions is relatively stable across these years (even though there are small hiccups here and there).


```r
average_salaries %>%
  ggplot(aes(x = year, y = (mean/1000000))) +
  geom_line() +
  theme_bw() +
  labs(
    title = "Change in Average Salaries in Positions",
    x = "Year", y = "Average Salaries (in Millions)") +
  facet_wrap(~ position, nrow = 2)
```

![](md_chapter4_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

Second, we see that even though much of the positions went through a salary hike, *Running Back* and *Special Teamer* has not increased much at all, at least compared to the increase in other positions.

Thank you!
