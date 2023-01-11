---
title: "Soc722 - MD Chapter 3"
author: "Turgut Keskintürk"
date: '2022-09-07'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---



Hello there! Let's start with our exercises for Chapter 3.

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
# loading the data
mario_kart <- read_csv("./data/world_records.csv")
```

```
## Rows: 2334 Columns: 9
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (6): track, type, shortcut, player, system_played, time_period
## dbl  (2): time, record_duration
## date (1): date
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
# have a quick look at the data
glimpse(mario_kart)
```

```
## Rows: 2,334
## Columns: 9
## $ track           <chr> "Luigi Raceway", "Luigi Raceway", "Luigi Raceway", "Lu…
## $ type            <chr> "Three Lap", "Three Lap", "Three Lap", "Three Lap", "T…
## $ shortcut        <chr> "No", "No", "No", "No", "No", "No", "No", "No", "No", …
## $ player          <chr> "Salam", "Booth", "Salam", "Salam", "Gregg G", "Rocky …
## $ system_played   <chr> "NTSC", "NTSC", "NTSC", "NTSC", "NTSC", "NTSC", "NTSC"…
## $ date            <date> 1997-02-15, 1997-02-16, 1997-02-16, 1997-02-28, 1997-…
## $ time_period     <chr> "2M 12.99S", "2M 9.99S", "2M 8.99S", "2M 6.99S", "2M 4…
## $ time            <dbl> 132.99, 129.99, 128.99, 126.99, 124.51, 122.89, 122.87…
## $ record_duration <dbl> 1, 0, 12, 7, 54, 0, 0, 27, 0, 64, 3, 0, 90, 132, 1, 74…
```

# Responses to Questions

Nice, we have the data. Let's start with the first question.

## Question 1

Let's first keep the records with type *Three Lap*. 


```r
three_laps <- mario_kart %>%
  filter(type == "Three Lap")
```

OK. We basically want to create two datasets: one without Rainbow Road track, and one that contains only that track. Let's go.


```r
# all tracks w/o rainbows
kart_neva_rainbows <- three_laps %>%
  filter(track != "Rainbow Road")

# only rainbow road track
kart_only_rainbows <- three_laps %>%
  filter(track == "Rainbow Road")
```

# Question 2

We are now going to compare the Rainbow Road track to the other tracks through the average time spent in the game.


```r
# summarize the rainbow road
kart_only_rainbows %>%
  summarize(
    mean = mean(time),
    sd = sd(time)
  )
```

```
## # A tibble: 1 × 2
##    mean    sd
##   <dbl> <dbl>
## 1  276.  91.8
```

```r
# summarize all the others
kart_neva_rainbows %>%
  summarize(
    mean = mean(time),
    sd = sd(time)
  )
```

```
## # A tibble: 1 × 2
##    mean    sd
##   <dbl> <dbl>
## 1  114.  53.0
```

We see that those that played in the Rainbow Road track had significantly higher time played compared to *all the other* tracks, meaning that what the exercises said about Rainbow Road not being a cool regular track seems to hold, no?

Yet, what if there are even **worse** tracks in that big lump? Let's go find out about that.

# Question 3

We are going to do two things: (a) we are gonna look at the established records for each track & (b) find out the most achieved track.


```r
three_laps %>%
  group_by(track) %>%
  summarise(n = n()) %>%
  arrange(desc(n))
```

```
## # A tibble: 16 × 2
##    track                     n
##    <chr>                 <int>
##  1 Toad's Turnpike         124
##  2 Rainbow Road             99
##  3 Frappe Snowland          92
##  4 D.K.'s Jungle Parkway    86
##  5 Choco Mountain           84
##  6 Mario Raceway            82
##  7 Luigi Raceway            81
##  8 Royal Raceway            77
##  9 Yoshi Valley             74
## 10 Kalimari Desert          73
## 11 Sherbet Land             73
## 12 Wario Stadium            71
## 13 Koopa Troopa Beach       56
## 14 Banshee Boardwalk        55
## 15 Moo Moo Farm             44
## 16 Bowser's Castle          40
```

We see that **Toad's Turnpike** has a lot of things going on!

# Question 4

OK, let's look at the drivers now.


```r
three_laps %>%
  group_by(player, track) %>%
  summarize(n = n()) %>%
  arrange(desc(n)) %>%
  head() # just to show the first couple of results.
```

```
## `summarise()` has grouped output by 'player'. You can override using the
## `.groups` argument.
```

```
## # A tibble: 6 × 3
## # Groups:   player [4]
##   player   track                     n
##   <chr>    <chr>                 <int>
## 1 Penev    Choco Mountain           26
## 2 Lacey    D.K.'s Jungle Parkway    24
## 3 abney317 Rainbow Road             21
## 4 MR       Toad's Turnpike          20
## 5 MR       Frappe Snowland          18
## 6 Penev    Toad's Turnpike          18
```

Nice! It seems that Penev scored 26 records in the Choco Mountain track. Good job, Penev!

# Question 5

This track and that track - we don't even know the averages yet! Let's look at them.


```r
three_laps %>%
  group_by(track) %>%
  summarise(mean = mean(time)) %>%
  arrange(desc(mean)) %>%
  head() # just to show the first couple of results.
```

```
## # A tibble: 6 × 2
##   track              mean
##   <chr>             <dbl>
## 1 Rainbow Road       276.
## 2 Wario Stadium      214.
## 3 Royal Raceway      158.
## 4 Bowser's Castle    134.
## 5 Kalimari Desert    126.
## 6 Banshee Boardwalk  126.
```

Well, yeah, Rainbow Road shines once more. Good. Let's look at the records one by one. 


```r
three_laps %>% 
  group_by(track) %>% 
  arrange(time) %>%
  slice(1) %>%
  select(track, time)
```

```
## # A tibble: 16 × 2
## # Groups:   track [16]
##    track                  time
##    <chr>                 <dbl>
##  1 Banshee Boardwalk     124. 
##  2 Bowser's Castle       132  
##  3 Choco Mountain         17.3
##  4 D.K.'s Jungle Parkway  21.4
##  5 Frappe Snowland        23.6
##  6 Kalimari Desert       122. 
##  7 Koopa Troopa Beach     95.2
##  8 Luigi Raceway          25.3
##  9 Mario Raceway          58.5
## 10 Moo Moo Farm           85.9
## 11 Rainbow Road           50.4
## 12 Royal Raceway         119. 
## 13 Sherbet Land           91.6
## 14 Toad's Turnpike        30.3
## 15 Wario Stadium          14.6
## 16 Yoshi Valley           33.4
```

Neat!

# Question 6

We are now going to create a binary variable that takes 1 if record_duration is higher than 100 and 0 otherwise.


```r
three_laps <- three_laps %>%
  mutate(
    records100days = as.integer(record_duration > 100)
  )
```

OK, which one of these awesome players hold the most number of records over 100 days?


```r
three_laps %>%
  group_by(player) %>%
  summarize(records = sum(records100days)) %>%
  arrange(desc(records)) %>%
  head()
```

```
## # A tibble: 6 × 2
##   player   records
##   <chr>      <int>
## 1 MR            81
## 2 MJ            50
## 3 Penev         27
## 4 abney317      26
## 5 VAJ           26
## 6 Zwartjes      24
```

Mr. MR seems to hold the best resume.

# Question 7

Let's load the drivers dataset.


```r
drivers <- read_csv("./Data/drivers.csv")
```

```
## Rows: 2250 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (2): player, nation
## dbl (4): position, total, year, records
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

Let's join the drivers dataset with our usual guys, using the player name. However, there are multiple observations in the drivers dataset, which naturally increases the row count with a left_join. I am going to trim the dataset first and then join.


```r
# trim the drivers dataset
drivers <- drivers %>%
  select(player, nation) %>%
  distinct()

# now, we are going to perform a left-join
three_laps <- three_laps %>%
  left_join(drivers, by = "player")
```

Let's plot the barchart for how many records there are.


```r
three_laps %>%
  drop_na() %>% # some missing observations
  group_by(nation) %>%
  summarize(n = n()) %>%
  ggplot(aes(x = reorder(nation, n), n)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  theme_bw() +
  labs(title = "The Number of Records for Each Countries",
       x = "Nation", y = "The # of Records") +
  coord_flip()
```

![](md_chapter3_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

Thank you!
