---
title: "Soc722 - MD Chapter 1"
author: "Turgut Keskintürk"
date: '2022-09-01'
output:
  html_document:
    theme: united
    toc: true
    number_sections: true
    keep_md: true
---

Hello there! Let's start with our exercises for Chapter 1.

# Question 1

Let's begin by installing some packages. I, too (just like Steve), don't want to bother with doing this all the time, so I'll modify the chunk options such that R will not evaluate this code block.


```r
install.packages("causact")
install.packages("dplyr")
install.packages("igraph")
```

Nicely done.

# Question 2

Now, we are going to load the packages that we have installed above. Let's do it.


```r
library("causact")
library("dplyr")
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library("igraph")
```

```
## 
## Attaching package: 'igraph'
```

```
## The following objects are masked from 'package:dplyr':
## 
##     as_data_frame, groups, union
```

```
## The following objects are masked from 'package:stats':
## 
##     decompose, spectrum
```

```
## The following object is masked from 'package:base':
## 
##     union
```

Got the warning messages as well! A quirky note: dplyr has a lot of similar names with other packages!

Let's run the code.


```r
# df <- as_data_frame(x = c(1, 2, 3))
```

OK, the warning told me that the x object I am trying to convert into a dataframe is not a graph object. Let's try the other one.


```r
df <- dplyr::as_data_frame(x = c(1, 2, 3))
```

```
## Warning: `as_data_frame()` was deprecated in tibble 2.0.0.
## Please use `as_tibble()` instead.
## The signature and semantics have changed, see `?as_tibble`.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.
```

```r
glimpse(df)
```

```
## Rows: 3
## Columns: 1
## $ value <dbl> 1, 2, 3
```

As expected, this one worked, for the simple reason that we specified which package we want for this specific function.

Let's go with another one.


```r
x <- c(5, 6, 2, 7, 9, 1)
dplyr::n_distinct(x)
```

```
## [1] 6
```

Nice! Using the package name before semi-colons, I made sure that we are calling the function from that specific package. 

It did not work in the earlier example because we loaded the igraph package after the dplyr package, which then changed the default function to be used. Remember that we got warnings such as "this function is masked" once we loaded the specific packages.

# Question 3

OK, let's look at the help function.


```r
# ?n_distinct
```

I see that n_distinct function takes a set or vector and counts the number of unique values in that set. Earlier, the x object contained 6 different values, and the output of the function was indeed 6.

# Question 4

Is baseball interesting? We should talk about it more! But, at least, data are always interesting!


```r
glimpse(baseballData)
```

```
## Rows: 12,145
## Columns: 5
## $ Date         <int> 20100405, 20100405, 20100405, 20100405, 20100405, 2010040…
## $ Home         <fct> ANA, CHA, KCA, OAK, TEX, ARI, ATL, CIN, HOU, MIL, NYN, PI…
## $ Visitor      <fct> MIN, CLE, DET, SEA, TOR, SDN, CHN, SLN, SFN, COL, FLO, LA…
## $ HomeScore    <int> 6, 6, 4, 3, 5, 6, 16, 6, 2, 3, 7, 11, 1, 3, 4, 2, 4, 3, 0…
## $ VisitorScore <int> 3, 0, 8, 5, 4, 3, 5, 11, 5, 5, 1, 5, 11, 5, 6, 1, 3, 6, 3…
```
OK, I see that the data has 12,145 rows, and 5 columns. I see that Home is a factor variable, while HomeScore is an integer.

# Question 5

Alright, baseball again! We can look at the first row.


```r
baseballData[1, ]
```

```
##       Date Home Visitor HomeScore VisitorScore
## 1 20100405  ANA     MIN         6            3
```

OK, I probably see a single baseball game here.


```r
baseballData[, 2:3] %>% head()
```

```
##   Home Visitor
## 1  ANA     MIN
## 2  CHA     CLE
## 3  KCA     DET
## 4  OAK     SEA
## 5  TEX     TOR
## 6  ARI     SDN
```

OK, here we see six different games, where the first column represents the home team while the second column represents the visitor team.

# Question 6

OK, I understand that we will build a dataframe ourselves. Let's go.


```r
name <-
  c(
    "Wayne Gretzky",
    "Gordie Howe",
    "Jaromir Jagr",
    "Brett Hull",
    "Marcel Dionne",
    "Phil Esposito" ,
    "Mike Gartner",
    "Alex Ovechkin",
    "Mark Messier" ,
    "Steve Yzerman"
  )

goals <- c(894, 801, 766, 741, 731, 717, 708, 700, 694, 692)

year_started <- c(1979, 1946, 1990, 1986, 1971, 1963, 1979, 2005, 1979, 1983)
```

Nicely done. Let's create a dataframe using the tibble function, and glimpse.


```r
df <- tibble(
  players = name,
  goals = goals,
  year_started = year_started
  ) 

glimpse(df)
```

```
## Rows: 10
## Columns: 3
## $ players      <chr> "Wayne Gretzky", "Gordie Howe", "Jaromir Jagr", "Brett Hu…
## $ goals        <dbl> 894, 801, 766, 741, 731, 717, 708, 700, 694, 692
## $ year_started <dbl> 1979, 1946, 1990, 1986, 1971, 1963, 1979, 2005, 1979, 1983
```

Thank you!
