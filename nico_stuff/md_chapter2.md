---
title: "Soc722 - MD Chapter 2"
author: "Turgut Keskintürk"
date: '2022-09-02'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---



Hello there! Let's start with our exercises for Chapter 2.

# Set-Up

First, we will begin with loading the tidyverse package and the dataset we will use for this exercise.


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

```r
# loading the data and having a glimpse on its content
olympics  <- read_csv(
  'https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-07-27/olympics.csv')
```

```
## Rows: 271116 Columns: 15
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (10): name, sex, team, noc, games, season, city, sport, event, medal
## dbl  (5): id, age, height, weight, year
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
glimpse(olympics)
```

```
## Rows: 271,116
## Columns: 15
## $ id     <dbl> 1, 2, 3, 4, 5, 5, 5, 5, 5, 5, 6, 6, 6, 6, 6, 6, 6, 6, 7, 7, 7, …
## $ name   <chr> "A Dijiang", "A Lamusi", "Gunnar Nielsen Aaby", "Edgar Lindenau…
## $ sex    <chr> "M", "M", "M", "M", "F", "F", "F", "F", "F", "F", "M", "M", "M"…
## $ age    <dbl> 24, 23, 24, 34, 21, 21, 25, 25, 27, 27, 31, 31, 31, 31, 33, 33,…
## $ height <dbl> 180, 170, NA, NA, 185, 185, 185, 185, 185, 185, 188, 188, 188, …
## $ weight <dbl> 80, 60, NA, NA, 82, 82, 82, 82, 82, 82, 75, 75, 75, 75, 75, 75,…
## $ team   <chr> "China", "China", "Denmark", "Denmark/Sweden", "Netherlands", "…
## $ noc    <chr> "CHN", "CHN", "DEN", "DEN", "NED", "NED", "NED", "NED", "NED", …
## $ games  <chr> "1992 Summer", "2012 Summer", "1920 Summer", "1900 Summer", "19…
## $ year   <dbl> 1992, 2012, 1920, 1900, 1988, 1988, 1992, 1992, 1994, 1994, 199…
## $ season <chr> "Summer", "Summer", "Summer", "Summer", "Winter", "Winter", "Wi…
## $ city   <chr> "Barcelona", "London", "Antwerpen", "Paris", "Calgary", "Calgar…
## $ sport  <chr> "Basketball", "Judo", "Football", "Tug-Of-War", "Speed Skating"…
## $ event  <chr> "Basketball Men's Basketball", "Judo Men's Extra-Lightweight", …
## $ medal  <chr> NA, NA, NA, "Gold", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…
```

```r
# medal column
table(olympics$medal)
```

```
## 
## Bronze   Gold Silver 
##  13295  13372  13116
```

# Responses to Questions

OK, let's go over the questions using the Olympic Gold Medalists dataset.

## Question 1

First, we will create a dataset which only includes the gold medalists, and count the number of medalists using this dataset.


```r
# filtering for gold medalists
gold_medalists <- olympics %>% 
  filter(medal == "Gold")

# counting the number of rows in the dataset
nrow(gold_medalists)
```

```
## [1] 13372
```

We see that there are **13,372** gold medalists in the resulting dataset.

## Question 2

Since x-variable ("year") has a sequential ordering, we actually need to have a sense of the changing age composition. 

However, there are lots of games in the dataset, which would make a general approach highly questionable. I would still show a scatterplot for participants in all sporting events with the caveat that we should look at this through different games.


```r
gold_medalists %>%
  drop_na() %>% # some missing values there
  ggplot(aes(x = year, y = age)) +
  geom_point(size = 1) +
  theme_bw() +
  labs(title = "Changing Age Composition of Gold Medalists",
       x = "Year", y = "Age")
```

![](md_chapter2_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

The plot gives us a sense of the changing age composition, though we do not know the actual distribution of these values in any particular year. Let us switch to a jittered plot to see the number of athletes in those particular years.


```r
gold_medalists %>%
  drop_na() %>%
  ggplot(aes(x = year, y = age)) +
  geom_jitter(size = 1, alpha = 0.25) +
  theme_bw() +
  labs(title = "Changing Age Composition of Gold Medalists",
       x = "Year", y = "Age")
```

![](md_chapter2_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

The jittered plot might help us solve the overplotting, but the shortening interval of data in the last years might also misguide the reader (see the Question 3 for the main reason for this). To have some *pretty* plot, I'd prefer using a boxplot, actually. Let's do it as well.


```r
gold_medalists %>%
  drop_na() %>%
  ggplot(aes(group = year, y = age)) +
  geom_boxplot(outlier.color = "gray", outlier.size = 1, fill = "steelblue") +
  theme_bw() +
  labs(title = "Changing Age Composition of Gold Medalists",
       x = "Year", y = "Age")
```

![](md_chapter2_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

## Question 3

OK, here is the US medalists for all years.


```r
us_medals <- gold_medalists %>%
  filter(noc == "USA") %>%
  group_by(year) %>%
  summarise(num_medals = n())
```

We want to plot the trajectory of the number of medals won by US teams as a line graph. Here is the code:


```r
us_medals %>%
  ggplot(aes(x = year, y = num_medals)) +
  geom_line(size = 0.5, color = "steelblue") +
  theme_bw() +
  labs(title = "The Number of Medals Won by US Teams Across Years",
       x = "Year", y = "The Number of Medals")
```

![](md_chapter2_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

As we can see from the graph (and the resulting dataset), the most successful year for the medals is 1984. 

The main reason behind the "wiggly" nature of the last couple of years seem to result from the changing intervals for the years (the dataframe starts with 4-year intervals, while after 1992, the intervals became 2-year).

I am really ignorant about sports, but not about Googling, so a simple search showed that there was a change from a 4-year-rotation to a 2-year-rotation for Winter and Summer Olympics. I am assuming that this affected the number of players for certain Olympic periods.

## Question 4

Here is the code for preparing the dataframe for the Gymnastics and 100 Meter Dash.


```r
two_events <- gold_medalists %>%
  filter(
    event == "Gymnastics Men's Individual All-Around"  |
      event == "Gymnastics Women's Individual All-Around" |
      event == "Athletics Women's 100 metres"   |
      event == "Athletics Men's 100 metres"
  )
```

Let's filter this dataset to create a Gymnastics event.


```r
gymnastics <- two_events %>%
  filter(
    event == "Gymnastics Men's Individual All-Around" |
      event == "Gymnastics Women's Individual All-Around"
  )
```

OK, we want to understand the distribution of age for Gymnastics events. Let's make a histogram for this.


```r
gymnastics %>%
  ggplot(aes(x = age)) +
  geom_histogram(binwidth = 1, color = "white", fill = "steelblue") +
  theme_bw() +
  labs(title = "The Age Distribution for Gymnastics Events",
       x = "Age", y = "# of Athletes")
```

![](md_chapter2_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

The distribution shows that the Gymnastics age can range from a little less than age 15 to nearly age 35. The majority of the responses are clustered around age 25 (a couple of more or less), and the histogram is kind of symmetric, though quickly descending around the tails.

Of course, this plot contains both woman and man athletes. Let us look at their differences.


```r
gymnastics %>%
  mutate(sex = case_when(
    sex == "M" ~ "Female", TRUE ~ "Male")) %>%
  ggplot(aes(x = age)) +
  geom_histogram(binwidth = 1, color = "white") +
  theme_bw() +
  labs(title = "The Age Distribution for Gymnastics Events",
       x = "Age", y = "# of Athletes") +
  facet_wrap(~ sex)
```

![](md_chapter2_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

It seems that male athletes tend to be older in Gymnastics.

## Question 5

OK, this section wants us to create boxplots to adjudicate which one of the events above has taller athletes.


```r
two_events %>%
  mutate(
    category = case_when(
      sex == "F" & sport == "Athletics" ~ "Female Athletics",
      sex == "F" & sport == "Gymnastics" ~ "Female Gymnastics",
      sex == "M" & sport == "Athletics" ~ "Male Athletics",
      sex == "M" & sport == "Gymnastics" ~ "Male Gymnastics",
      TRUE ~ "NA"
    )
  ) %>%
  ggplot(aes(x = category, y = height)) +
  geom_boxplot(fill = "steelblue") +
  theme_bw() +
  labs(
    title = "The Height Across Two Sports & Sex", x = "Groups", y = "Height")
```

```
## Warning: Removed 10 rows containing non-finite values (stat_boxplot).
```

![](md_chapter2_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
 
It seems that athletes at the Athletics event are taller than the ones in the Gymnastic event.

## Question 6

OK, in this last section (phew!), we want to understand the portion of male and female athletes for the winners. Let's create the dataset.


```r
us_medalists <- gold_medalists %>% 
  filter(noc == "USA")
```
 
And now we are going to make a barplot.


```r
us_medalists %>%
  ggplot(aes(x = sex)) + 
  geom_bar(position = "dodge", fill = "steelblue") +
  theme_bw() +
  labs(title = "The Female and Male Athletes in US Medalists",
       x = "Sex", y = "The # of People")
```

![](md_chapter2_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

We see that there are a lot more male medalist athletes than female athletes.

Thank you!

