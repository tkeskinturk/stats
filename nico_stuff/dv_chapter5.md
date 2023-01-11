---
title: "Soc722 - DV Chapters 5"
author: "Turgut Keskintürk"
date: '2022-09-15'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---



Hello there! Let's start with our exercises for Kieran Healy's Data Visualization, Chapter 5.

# Set-Up


```r
# load the best package in the world and some others
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
library(ggrepel)

# read in the data 
wncaa <- read_csv("./data/wncaa.csv")
```

```
## Rows: 2092 Columns: 19
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (6): school, conference, conf_place, how_qual, x1st_game_at_home, tourn...
## dbl (13): year, seed, conf_w, conf_l, conf_percent, reg_w, reg_l, reg_percen...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
# glimpse the data 
glimpse(wncaa)
```

```
## Rows: 2,092
## Columns: 19
## $ year              <dbl> 1982, 1982, 1982, 1982, 1982, 1982, 1982, 1982, 1982…
## $ school            <chr> "Arizona St.", "Auburn", "Cheyney", "Clemson", "Drak…
## $ seed              <dbl> 4, 7, 2, 5, 4, 6, 5, 8, 7, 7, 4, 8, 2, 1, 1, 2, 3, 6…
## $ conference        <chr> "Western Collegiate", "Southeastern", "Independent",…
## $ conf_w            <dbl> NA, NA, NA, 6, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
## $ conf_l            <dbl> NA, NA, NA, 3, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
## $ conf_percent      <dbl> NA, NA, NA, 66.7, NA, NA, NA, NA, NA, NA, NA, NA, NA…
## $ conf_place        <chr> "-", "-", "-", "4th", "-", "-", "-", "-", "-", "-", …
## $ reg_w             <dbl> 23, 24, 24, 20, 26, 19, 21, 14, 21, 28, 24, 17, 22, …
## $ reg_l             <dbl> 6, 4, 2, 11, 6, 7, 8, 10, 8, 7, 5, 13, 7, 5, 1, 6, 4…
## $ reg_percent       <dbl> 79.3, 85.7, 92.3, 64.5, 81.3, 73.1, 72.4, 58.3, 72.4…
## $ how_qual          <chr> "at-large", "at-large", "at-large", "at-large", "aut…
## $ x1st_game_at_home <chr> "Y", "N", "Y", "N", "Y", "N", "N", "N", "N", "N", "Y…
## $ tourney_w         <dbl> 1, 0, 4, 0, 2, 0, 0, 0, 0, 0, 2, 0, 2, 1, 5, 3, 1, 1…
## $ tourney_l         <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1…
## $ tourney_finish    <chr> "RSF", "1st", "N2nd", "1st", "RF", "1st", "1st", "1s…
## $ full_w            <dbl> 24, 24, 28, 20, 28, 19, 21, 14, 21, 28, 26, 17, 24, …
## $ full_l            <dbl> 7, 5, 3, 12, 7, 8, 9, 11, 9, 8, 6, 14, 8, 6, 1, 7, 5…
## $ full_percent      <dbl> 77.4, 82.8, 90.3, 62.5, 80.0, 70.4, 70.0, 56.0, 70.0…
```

# Responses to Questions

## Question 1

OK, we are going to start with some summarizing tricks.


```r
champs <- wncaa %>%
  filter(tourney_finish == "Champ") %>%
  group_by(school) %>%
  summarize(n = n()) %>%
  mutate(per_tour = (n / sum(n))*100)

champs %>%
  ggplot(aes(x = reorder(school, per_tour), per_tour)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  theme_bw() +
  labs(title = "The Percentage of Tournaments by School",
       x = "School", y = "Percentage of Tournaments") +
  coord_flip()
```

![](dv_chapter5_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

It seems that **UConn** and **Tennessee** just rock the show together!

## Question 2

Let's first get the names of the champions and create a dataset.


```r
# get the names

champ_names <- unique(champs$school) # Aha, good old dolar sign!

# filter the original dataset

winners <- wncaa %>% 
  filter(school %in% champ_names)
```

Before going to the plots, let's include the average seed for the teams.


```r
winners <- winners %>%
  group_by(school) %>%
  summarise(av_seed = mean(seed)) %>%
  ungroup() %>%
  left_join(winners, by = "school")
```


OK, nice. We are going to create a boxplot for the seeds to understand how winners start the seasons.


```r
winners %>%
  ggplot(aes(x = reorder(school, av_seed), seed)) +
  geom_boxplot(fill = "steelblue", col = "black") +
  theme_bw() +
  labs(title = "The Seed Distributions Across Schools",
       x = "Schools", y = "Seed") +
  coord_flip()
```

![](dv_chapter5_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Hmm... I had no idea what a seed meant before Googling. Then it makes sense that Tennessee and UConn take the winner seeds, right?

OK, the best ones on the road: violin plots!


```r
winners %>%
  ggplot(aes(x = reorder(school, av_seed), seed)) +
  geom_violin(fill = "steelblue", col = "black") +
  theme_bw() +
  labs(title = "The Seed Distributions Across Schools",
       x = "Schools", y = "Seed") +
  coord_flip()
```

![](dv_chapter5_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

Meh. Usually, violin plots are really informative, though this time they look pretty ugly.

## Question 3

OK, same plot, but with points.


```r
winners %>%
  ggplot(aes(x = reorder(school, av_seed), seed)) +
  geom_point() +
  theme_bw() +
  labs(title = "The Seed Distributions Across Schools",
       x = "Schools", y = "Seed") +
  coord_flip()
```

![](dv_chapter5_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

Well, *overplotting* makes us bored with a lot of things. Let's jitter?

## Question 4

Alright, now we are going to use ``summarize_if`` to calculate the mean and standard deviation.


```r
winners_summarized <- winners %>%
  group_by(school) %>%
  summarize_if(
    is.numeric, list(mean = mean, sd = sd), na.rm = TRUE
  ) %>%
  ungroup()
```

OK, we have the data. We are going to make a dot plot.


```r
winners_summarized %>%
  ggplot(aes(x = reg_percent_mean, 
             y = reorder(school, reg_percent_mean))) + 
  geom_point() +
  theme_bw() +
  labs(title = "Mean of the Winners",
       x = "School", y = "Mean Value")
```

![](dv_chapter5_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

UConn shows the most percentage of mean for the seasons. Let's add the standard deviations as well.


```r
winners_summarized %>%
  ggplot(aes(x = reg_percent_mean, 
             y = reorder(school, reg_percent_mean))) +
  geom_pointrange(aes(xmin = reg_percent_mean - reg_percent_sd,
                      xmax = reg_percent_mean + reg_percent_sd)) +
  theme_bw() +
  labs(title = "Mean of the Winners",
       x = "School", y = "Mean Value")
```

![](dv_chapter5_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

Neat! We can always do this with ``geom_linerange``, too. Let's try.


```r
winners_summarized %>%
  ggplot(aes(x = reg_percent_mean, 
             y = reorder(school, reg_percent_mean))) +
  geom_point() +
  geom_linerange(aes(xmin = reg_percent_mean - reg_percent_sd,
                     xmax = reg_percent_mean + reg_percent_sd)) +
  theme_bw() +
  labs(title = "Mean of the Winners",
       x = "School", y = "Mean Value")
```

![](dv_chapter5_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

We added the ``geom_point`` as well, to keep the mean points.

## Question 5

OK, we are now trying to understand the relationship between the performance in the tournament and the full performance.


```r
winners %>%
  ggplot(aes(reg_percent, y = full_percent)) +
  geom_point() +
  geom_abline() +
  labs(title = "Relationship Between Tournament Performance and Full Performance",
       x = "Tournament Percentage", y = "Full Percentage") +
  theme_bw()
```

![](dv_chapter5_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

We see that most teams are below the line, but there are some that become good after the tournament is over.

## Question 6

Let's make a variable that takes 1 if that team ended up winning the tournament or a 0 otherwise.


```r
winners <- winners %>% 
  mutate(is_champ = if_else(tourney_finish == "Champ", 1, 0), 
         is_champ = as.factor(is_champ))
```

OK, we are going to color the plot above to understand the performance of the tournament winners.


```r
winners %>%
  ggplot(aes(reg_percent, y = full_percent, group = is_champ, col = is_champ)) +
  geom_point() +
  geom_abline() +
  labs(title = "Relationship Between Tournament Performance and Full Performance",
       x = "Tournament Percentage", y = "Full Percentage") +
  theme_bw() + labs(color = "Champion?") + theme(legend.position = "top")
```

![](dv_chapter5_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

``is_champ`` should be a factor because if we feed this variable to the ``ggplot`` as numeric, the legends (and colors) will be plotted as if the variable itself is continuous. Instead, we want to understand the winners and non-winners, as two categories.

Yes, it seems that champions become much better. This makes sense.

## Question 7

We are now going to label these points. Let's create the variables needed.


```r
# labels

winners <- winners %>% 
  mutate(plot_label = paste(school, year, sep = "-"))

# performance difference

winners <- winners %>% 
  mutate(difference = full_percent - reg_percent) 
```

OK, let's look at the points now.


```r
winners %>%
  ggplot(aes(reg_percent, y = full_percent, 
             group = is_champ, col = is_champ)) +
  geom_point() +
  geom_abline() +
  geom_text_repel(data = subset(winners,
                                # some subset conditions
                                (reg_percent < 50 & full_percent < 50) 
                                |
                                (reg_percent < 75 & full_percent < 75 & is_champ == 1)),
                  mapping = aes(label = plot_label)) +
  labs(title = "Relationship Between Tournament Performance and Full Performance",
       x = "Tournament Percentage", y = "Full Percentage") +
  theme_bw() + labs(color = "Champion?") + theme(legend.position = "top")
```

![](dv_chapter5_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

Yes, **Tennessee** killed!

## Question 8


```r
winners %>%
  filter(full_percent == 100 & reg_percent == 100) %>%
  select(school, year)
```

```
## # A tibble: 8 × 2
##   school  year
##   <chr>  <dbl>
## 1 Baylor  2012
## 2 Texas   1986
## 3 UConn   1995
## 4 UConn   2002
## 5 UConn   2009
## 6 UConn   2010
## 7 UConn   2014
## 8 UConn   2016
```

Well, UConn is everywhere. Cool!

Thank you!

