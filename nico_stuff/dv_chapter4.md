---
title: "Soc722 - DV Chapters 4"
author: "Turgut Keskintürk"
date: '2022-09-13'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---



Hello there! Let's start with our exercises for Kieran Healy's Data Visualization, Chapter 4.

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
# read in the data 
tv_ratings <- read_csv("./data/tv_ratings.csv")
```

```
## Rows: 2266 Columns: 7
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (3): titleId, title, genres
## dbl  (3): seasonNumber, av_rating, share
## date (1): date
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
# glimpse the data 
glimpse(tv_ratings)
```

```
## Rows: 2,266
## Columns: 7
## $ titleId      <chr> "tt2879552", "tt3148266", "tt3148266", "tt3148266", "tt31…
## $ seasonNumber <dbl> 1, 1, 2, 3, 4, 1, 2, 1, 2, 3, 4, 5, 6, 7, 8, 1, 1, 1, 1, …
## $ title        <chr> "11.22.63", "12 Monkeys", "12 Monkeys", "12 Monkeys", "12…
## $ date         <date> 2016-03-10, 2015-02-27, 2016-05-30, 2017-05-19, 2018-06-…
## $ av_rating    <dbl> 8.4890, 8.3407, 8.8196, 9.0369, 9.1363, 8.4370, 7.5089, 8…
## $ share        <dbl> 0.51, 0.46, 0.25, 0.19, 0.38, 2.38, 2.19, 6.67, 7.13, 5.8…
## $ genres       <chr> "Drama,Mystery,Sci-Fi", "Adventure,Drama,Mystery", "Adven…
```

# Responses to Questions

## Question 1

OK, we are first going to keep those shows with 5 or more seasons. Let's use Nico's code.


```r
tv_long <- tv_ratings %>% 
  group_by(title) %>% 
  summarise(num_seasons = n()) %>% 
  ungroup() %>% 
  left_join(tv_ratings, by = "title") 

tv_long <- tv_long %>% 
  filter(num_seasons >= 5)
```

We now try to draw a line graph for each show's rating trajectories.


```r
tv_long %>%
  ggplot(aes(x = seasonNumber, y = av_rating, group = title)) +
  geom_line() +
  theme_bw() +
  labs(title = "The Change of Average Rating Across Seasons",
       x = "Season Number", y = "Average IMDB Rating")
```

![](dv_chapter4_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

This is really really messy, and nearly impossible to read. We need another method here.

## Question 2

Let's facet the plot by genre to see the patterns more clearly. I am going to play with the image output to have better results.


```r
tv_long %>%
  ggplot(aes(x = seasonNumber, y = av_rating, group = title)) +
  geom_line() +
  theme_bw() +
  labs(title = "The Change of Average Rating Across Seasons",
       x = "Season Number", y = "Average IMDB Rating") +
  facet_wrap(~ genres)
```

![](dv_chapter4_files/figure-html/fig-1.png)<!-- -->

It seems that the shows in *Drama, Romance* and *Crime, Drama, Mystery* tend to last longer. If we look past the rating differences, there is a slight tendency to drop, but most notably in genres as *Drama, Family, Fantasy* and *Drama, Horror, Sci-Fi*.

Let's look at the show in the *Drama, Family, Fantasy*.


```r
tv_long %>%
  filter(genres == "Drama,Family,Fantasy") %>%
  select(title) %>%
  unique()
```

```
## # A tibble: 1 × 1
##   title                      
##   <chr>                      
## 1 Are You Afraid of the Dark?
```

The show in question is **Are You Afraid of the Dark?** Too bad.

## Question 3

We are now going to look at the best shows and their corresponding genres.


```r
tv_ratings %>%
  filter(av_rating >= 9) %>%
  group_by(genres) %>%
  summarize(n = n()) %>%
  ggplot(aes(x = reorder(genres, n), n)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  coord_flip() +
  theme_bw() +
  labs(title = "Average Rating of Best Shows Across Genres",
       x = "Genres", y = "The Number of Shows")
```

![](dv_chapter4_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

The genre with the top-rated TV shows is **Drama**. The code ``coord_flip`` helped us flip the Cartesian coordinates.

## Question 4

Let's give Nico the ride for the following code:


```r
comedies_dramas <- tv_ratings %>% 
  mutate(is_comedy = if_else(str_detect(genres, "Comedy"), 
                             1, 
                             0)) %>% # If it contains the word comedy then 1, else 0
  filter(is_comedy == 1 | genres == "Drama") %>% # Keep comedies and dramas
  mutate(genres = if_else(genres == "Drama", # Make it so that we only have those two genres
                          "Drama", 
                          "Comedy"))

glimpse(comedies_dramas)
```

```
## Rows: 684
## Columns: 8
## $ titleId      <chr> "tt0312081", "tt0312081", "tt0312081", "tt1225901", "tt12…
## $ seasonNumber <dbl> 1, 2, 3, 1, 2, 3, 4, 5, 1, 2, 1, 25, 1, 1, 2, 3, 4, 5, 1,…
## $ title        <chr> "8 Simple Rules", "8 Simple Rules", "8 Simple Rules", "90…
## $ date         <date> 2002-09-17, 2003-11-04, 2004-11-12, 2009-01-03, 2009-11-…
## $ av_rating    <dbl> 7.5000, 8.6000, 8.4043, 7.1735, 7.4686, 7.6858, 6.8344, 7…
## $ share        <dbl> 0.03, 0.10, 0.06, 0.40, 0.14, 0.10, 0.04, 0.01, 0.48, 0.4…
## $ genres       <chr> "Comedy", "Comedy", "Comedy", "Comedy", "Comedy", "Comedy…
## $ is_comedy    <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, …
```

Let's plot a density plot for seeing the average differences.


```r
comedies_dramas %>%
  ggplot(aes(x = av_rating, group = genres, fill = genres)) +
  geom_density(alpha = 0.25) +
  theme_bw() +
  labs("Differences Between Comedy and Drama",
       x = "Average Rating", y = "Density") +
  theme(legend.position = "bottom", legend.title = element_blank())
```

![](dv_chapter4_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

Yes! It seems that comedies and dramas have similar rating scores. In fact, the variation of drama series seem much higher than the ones in the comedy group, suggesting that comedies are good, and they are *generally* good.

Yet, it seems that there are less drama series than the comedies. Is it true?

## Question 5

OK, let's make the same plot using histograms.


```r
comedies_dramas %>%
  ggplot(aes(x = av_rating, group = genres, fill = genres)) +
  geom_histogram(alpha = 0.5) +
  theme_bw() +
  labs("Differences Between Comedy and Drama",
       x = "Average Rating", y = "Density") +
  theme(legend.position = "bottom", legend.title = element_blank())
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](dv_chapter4_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Well, there are *a lot* more comedies than the drama series in the dataset. This is probably because when we filter the shows with Drama, we excluded a lot of shows that included Drama and some other genre as well.


```r
comedies_dramas %>%
  ggplot(aes(x = av_rating, group = genres, color = genres)) +
  geom_freqpoly(show.legend = TRUE) +
  theme_bw() +
  labs("Differences Between Comedy and Drama",
       x = "Average Rating", y = "Frequency") +
  theme(legend.position = "bottom", legend.title = element_blank())
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](dv_chapter4_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Meh, I particularly don't like the frequency plots, though I can acknowledge that it helps us a lot to see the basic differences. It basically takes the visual simplicity of the density plots and supplement the histogram's power of showing the counts.

## Question 6

We are going to examine the relationship between average rating and share of viewership.


```r
comedies_dramas %>%
  ggplot(aes(x = av_rating, y = share)) +
  geom_bin_2d() +
  theme_bw() +
  labs(x = "Average Rating", y = "Share")
```

![](dv_chapter4_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

Nice. This is basically a heatmap, where each ractangle is shaded according to the number of observations there. It seems that most shows have really low sharing, and the connection between ratings and sharings is not directly linear (though a couple of outliers there).

Let's separate the genres.


```r
comedies_dramas %>%
  ggplot(aes(x = av_rating, y = share, fill = genres)) +
  geom_bin_2d() +
  theme_bw() +
  labs(x = "Average Rating", y = "Share") +
  theme(legend.position = "bottom", legend.title = element_blank())
```

![](dv_chapter4_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

Aha! This made it better. Now we see that there is an outlier in the Drama category. 


```r
comedies_dramas %>%
  filter(genres == "Drama") %>%
  arrange(desc(share)) %>%
  head()
```

```
## # A tibble: 6 × 8
##   titleId   seasonNumber title          date       av_rat…¹ share genres is_co…²
##   <chr>            <dbl> <chr>          <date>        <dbl> <dbl> <chr>    <dbl>
## 1 tt0092337            1 Dekalog        1990-04-13     8.22 27.2  Drama        0
## 2 tt0200276            1 The West Wing  1999-12-14     8.61  5.43 Drama        0
## 3 tt0200276            2 The West Wing  2001-01-11     8.84  4.37 Drama        0
## 4 tt0200276            4 The West Wing  2003-01-12     8.29  2.84 Drama        0
## 5 tt0804503            1 Mad Men        2007-09-01     8.23  2.81 Drama        0
## 6 tt1856010            1 House of Cards 2013-02-01     8.56  2.8  Drama        0
## # … with abbreviated variable names ¹​av_rating, ²​is_comedy
```

Wow, **Dekalog** rocked the show.

Thank you!

