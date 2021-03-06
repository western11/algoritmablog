---
title: 'Rplicate Series: I can''t get no ...'
author: David Tahi Ulubalang
github: https://github.com/Davidlimbong
date: '2020-10-26'
output:
  blogdown::html_page:
    toc: true
    highlight: breezedark
slug: rplicate-i-can-t-get-no
categories:
  - R
tags: 
  - Data Visualization
  - Rplicate
  - ggplot2
description: ''
featured: ''
featuredalt: ''
featuredpath: ''
linktitle: ''
type: post
---



In this third article on **Rplicate Series**, We will share to you one way to replicate The Economist plot titled **"I can't get no ..."**. The plot making will also demonstrate ways to converting smooth line into **linear line**, using **guides for legend**, and adding a **text annotation** to a plot. The raw dataset used for this graph was obtained from a PDF, so we will also use **tibble** package to copy-paste the data into R.

<center> ![](/img/rplicate3/icant.png){width="60%"} </center>

Let's dive in below!

# Load Packages

These are the basic packages that we will use. Along the way, there will be some packages that we will load just before its use to better understand and remember its functionality.


```r
library(dplyr)
library(ggplot2)
```

# Dataset

## Data Collection

The data used in this plot is sourced from a PDF file. Fortunately, we can literally *copy-paste* the data using `tribble()` from _tibble_ package. Click [here](https://tibble.tidyverse.org/reference/tribble.html) for more detailed documentation of tribble.


```r
library(tibble)
data1 <- tribble(
              ~V1, ~V2, ~V3, ~V4, ~V5,
  "United States", 47L, 58L, 56L, 61L,
         "Canada", 73L, 79L, 78L, 82L,
         "France", 47L, 54L, 62L, 73L,
        "Germany", 72L, 77L, 75L, 85L,
         "Greece", 18L, 25L, 22L, 42L,
        "Hungary", 42L, 49L, 54L, 63L,
          "Italy", 36L, 46L, 45L, 60L,
    "Netherlands", 74L, 82L, 82L, 89L,
         "Poland", 40L, 43L, 43L, 59L,
          "Spain", 33L, 48L, 48L, 63L,
         "Sweden", 66L, 78L, 78L, 86L,
             "UK", 52L, 64L, 63L, 74L,
         "Russia", 55L, 68L, 60L, 79L,
      "Australia", 48L, 60L, 57L, 65L,
          "India", 65L, 72L, 80L, 72L,
      "Indonesia", 77L, 85L, 85L, 89L,
          "Japan", 55L, 55L, 65L, 74L,
    "Philippines", 78L, 83L, 86L, 87L,
    "South Korea", 27L, 26L, 36L, 44L,
        "Vietnam", 78L, 78L, 80L, 85L,
         "Israel", 42L, 50L, 63L, 78L,
         "Jordan", 46L, 47L, 55L, 60L,
        "Lebanon", 36L, 62L, 48L, 73L,
        "Tunisia", 48L, 52L, 56L, 58L,
         "Turkey", 57L, 73L, 65L, 74L,
          "Ghana", 68L, 70L, 77L, 81L,
          "Kenya", 70L, 75L, 80L, 82L,
        "Nigeria", 67L, 68L, 71L, 74L,
        "Senegal", 75L, 75L, 72L, 84L,
   "South Africa", 65L, 69L, 73L, 76L,
       "Tanzania", 83L, 89L, 93L, 92L,
      "Argentina", 37L, 38L, 45L, 56L,
         "Brazil", 45L, 54L, 57L, 66L,
          "Chile", 36L, 36L, 42L, 55L,
       "Colombia", 38L, 43L, 41L, 57L,
         "Mexico", 58L, 55L, 62L, 68L,
           "Peru", 42L, 42L, 51L, 59L,
      "Venezuela", 52L, 50L, 58L, 62L
  )

data2 <- tribble(
               ~V1,            ~V2, ~V3, ~V4, ~V5, ~V6, ~V7,  ~V8,
   "United States", "Spring, 2017", 15L, 36L, 24L, 23L,  2L, 100L,
          "Canada", "Spring, 2017", 20L, 47L, 18L, 14L,  1L, 100L,
          "France", "Spring, 2017",  3L, 17L, 36L, 42L,  1L, 100L,
         "Germany", "Spring, 2017", 26L, 43L, 19L, 10L,  1L, 100L,
          "Greece", "Spring, 2017",  1L, 12L, 26L, 60L,  1L, 100L,
         "Hungary", "Spring, 2017",  9L, 48L, 25L, 16L,  2L, 100L,
           "Italy", "Spring, 2017",  1L, 25L, 47L, 26L,  2L, 100L,
     "Netherlands", "Spring, 2017", 24L, 47L, 20L,  9L,  1L, 100L,
          "Poland", "Spring, 2017", 14L, 28L, 31L, 24L,  4L, 100L,
           "Spain", "Spring, 2017",  5L, 12L, 40L, 41L,  1L, 100L,
          "Sweden", "Spring, 2017", 15L, 52L, 23L, 10L,  0L, 100L,
  "United Kingdom", "Spring, 2017", 14L, 35L, 28L, 20L,  4L, 100L,
          "Russia", "Spring, 2017", 20L, 47L, 24L,  6L,  3L, 100L,
       "Australia", "Spring, 2017",  7L, 41L, 29L, 21L,  2L, 100L,
           "India", "Spring, 2017", 39L, 46L,  7L,  4L,  4L, 100L,
       "Indonesia", "Spring, 2017", 30L, 53L, 15L,  1L,  0L, 100L,
           "Japan", "Spring, 2017",  6L, 51L, 33L,  7L,  3L, 100L,
     "Philippines", "Spring, 2017", 36L, 44L, 16L,  4L,  0L, 100L,
     "South Korea", "Spring, 2017",  2L, 21L, 50L, 25L,  2L, 100L,
         "Vietnam", "Spring, 2017", 31L, 51L, 16L,  1L,  1L, 100L,
          "Israel", "Spring, 2017", 14L, 37L, 33L, 14L,  2L, 100L,
          "Jordan", "Spring, 2017", 13L, 35L, 30L, 18L,  3L, 100L,
         "Lebanon", "Spring, 2017",  2L, 13L, 42L, 43L,  1L, 100L,
         "Tunisia", "Spring, 2017", 11L, 18L, 14L, 56L,  2L, 100L,
           "Ghana", "Spring, 2017", 51L, 19L, 13L, 13L,  4L, 100L,
           "Kenya", "Spring, 2017", 28L, 40L, 20L, 12L,  1L, 100L,
         "Nigeria", "Spring, 2017", 34L, 20L, 21L, 24L,  1L, 100L,
         "Senegal", "Spring, 2017", 37L, 23L, 22L, 16L,  2L, 100L,
    "South Africa", "Spring, 2017", 22L, 22L, 28L, 24L,  3L, 100L,
        "Tanzania", "Spring, 2017", 48L, 41L,  5L,  4L,  1L, 100L,
       "Argentina", "Spring, 2017", 10L, 12L, 41L, 34L,  3L, 100L,
          "Brazil", "Spring, 2017",  2L, 22L, 18L, 56L,  3L, 100L,
           "Chile", "Spring, 2017",  5L, 10L, 51L, 32L,  2L, 100L,
        "Colombia", "Spring, 2017",  7L,  5L, 51L, 36L,  1L, 100L,
          "Mexico", "Spring, 2017",  2L, 15L, 39L, 43L,  1L, 100L,
            "Peru", "Spring, 2017",  4L,  8L, 55L, 31L,  2L, 100L,
       "Venezuela", "Spring, 2017", 14L, 15L, 24L, 45L,  2L, 100L
  )

# adding colnames to data1
colnames(data1) <- c("Country","Political_issues", "News about govt. leaders and officials","News accurately","Most important news events")

# adding colnames to data2
colnames(data2) <- c("Country","x", "A lot", "Somewhat", "Not much", "Not at all", "DK/Refused", "Total")
```

Below is a glimpse of our data:


```r
head(data1, 3)
```

```
#> # A tibble: 3 x 5
#>   Country  Political_issues `News about govt.~ `News accuratel~ `Most important~
#>   <chr>               <int>              <int>            <int>            <int>
#> 1 United ~               47                 58               56               61
#> 2 Canada                 73                 79               78               82
#> 3 France                 47                 54               62               73
```



```r
head(data2, 3)
```

```
#> # A tibble: 3 x 8
#>   Country    x       `A lot` Somewhat `Not much` `Not at all` `DK/Refused` Total
#>   <chr>      <chr>     <int>    <int>      <int>        <int>        <int> <int>
#> 1 United St~ Spring~      15       36         24           23            2   100
#> 2 Canada     Spring~      20       47         18           14            1   100
#> 3 France     Spring~       3       17         36           42            1   100
```

## Feature Selection

There are some features that we need to add and also select for visualization. But before that, we should first join our data. Here, we can join our data using the function `merge()` from base R.


```r
# join data
dataset <- merge(data1, data2, by = "Country")
```


```r
# feature engineering `gov`
data_f <- dataset %>% 
  mutate(gov = `A lot` + Somewhat) %>% 
  select(Country,Political_issues,gov)

head(data_f, 3)
```

```
#>     Country Political_issues gov
#> 1 Argentina               37  22
#> 2 Australia               48  48
#> 3    Brazil               45  24
```


```r
# preparing region-country labels
Asia_Oceania <- c("Australia", "India", "Indonesia", "Japan",
                  "Philippines", "South Korea", "Vietnam")
Europe <- c("France", "Germany", "Greece", "Hungary", "Italy",
            "Netherlands", "Poland", "Spain", "Sweden", "UK", "Russia")
MidEast_NAfrica <- c("Israel", "Jordan", "Lebanon", 
                            "Tunisia", "Turkey")
SouthAmerica <- c("Argentina", "Brazil", "Chile", 
                  "Colombia", "Mexico", "Peru", "Venezuela")
SubSaharaAfrica <- c("Ghana", "Kenya", "Nigeria", 
                     "Senegal", "South Africa", "Tanzania")
NorthAmerica <- c("United States", "Canada")

# feature engineering `region`
data_f <- data_f %>% 
  mutate(region = ifelse(data_f$Country %in% Asia_Oceania, 'Asia & Oceania',
          ifelse(data_f$Country %in% Europe, 'Europe',
          ifelse(data_f$Country %in% MidEast_NAfrica, 'Middle East & North Africa',
          ifelse(data_f$Country %in% SouthAmerica, 'South America',
          ifelse(data_f$Country %in% SubSaharaAfrica, 'Sub-Sahara Africa',
          ifelse(data_f$Country %in% NorthAmerica, 'North America',
          NA)))))))

# quick check
head(data_f, 3)
```

```
#>     Country Political_issues gov         region
#> 1 Argentina               37  22  South America
#> 2 Australia               48  48 Asia & Oceania
#> 3    Brazil               45  24  South America
```

# Plotting

Before making anything, we can first load fonts from our locale to R using **extrafont** package. This will allow R to use custom fonts in its plot. Discussions about using custom font can be found in the previous [Rplicate Series articles](https://algotech.netlify.app/tags/rplicate/).


```r
library(extrafont)

# importing fonts to R
# font_import() 

# load all fonts so it can be used
loadfonts(device = "win") 
```

## Basic Plot

We can first make the Basic Plot using the code below:


```r
g1 <- ggplot(data = data_f, 
             aes(x = gov, y = Political_issues)) +
  geom_point(aes(colour = region, fill = region), # we will edit the color & fill manually later
             shape = 21, size = 3.5, stroke = 1) +
  scale_x_continuous(breaks = seq(0,100, by=20), limits = c(0,100)) +
  scale_y_continuous(breaks = seq(0,100, by=20), limits = c(0,100),
                     expand = expansion(add = c(0, 5))) # adjusting spaces on the min-max y-axis)

g1
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-9-1.png" width="672" style="display: block; margin: auto;" />

## Add Linear Line

This is the more interesting part of making the plot. When using `geom_smooth()`, we usually aim to display a trend line following pattern in our data and ended up obtaining a slightly curved line. Little that we know, this function can also be used for _making a linear line_, we only need to adjust its parameter `method` on how to make the trend line using a specific algorithm.


```r
g2 <- g1 + 
  geom_smooth(aes(group = 1), # making 1 line
              method = "lm", # making a linear plot (using linear model)
              # formula = y ~ x, # we can also edit the linear model formula manually here
              se = FALSE, # not displaying standard error
              color = "#77200b") # color of line 

g2
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-10-1.png" width="672" style="display: block; margin: auto;" />

Now, the trend line was made but it disturb our view on the other geoms. It is best to rearrange the geoms so that the trend line positioned in the first layer of the plot. The combined codes from the previous steps are below and we will use it for further process.


```r
g2 <- ggplot(data = data_f, 
             aes(x = gov, y = Political_issues)) +
  geom_smooth(aes(group = 1), 
              method = "lm", 
              se = FALSE, 
              color = "#77200b") +
  geom_point(aes(colour = region, fill = region), 
             shape = 21, size = 3.5, stroke = 1) +
  scale_x_continuous(breaks = seq(0,100, by=20), limits = c(0,100)) +
  scale_y_continuous(breaks = seq(0,100, by=20), limits = c(0,100),
                     expand = expansion(add = c(0, 5))) 

g2
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-11-1.png" width="672" style="display: block; margin: auto;" />

## Add Repelled Text

Just like in the previous ["Rplicate Series: Happiness of The Third Age"](https://algotech.netlify.app/blog/rplicate-happiness-of-the-third-age/), we can add repelled text label using `geom_text_repel()` from package _ggrepel_.


```r
library(ggrepel)
g3 <- g2 + geom_text_repel(aes(label = Country), force = 2,
                           color = "grey20", size = 3, family = "Segoe UI")

g3
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-12-1.png" width="672" style="display: block; margin: auto;" />

## Add Labels & Title


```r
g4 <- g3 + 
  labs(title = "I can't get no...",
       subtitle = "Trust in goverment and satifaction with the media, 2017",
       x = "Trust the national goverment to do what is right for our country, % of respondents",
       y = "Satisfied with the news media in our country*, % of respondents",
       caption = "Source: Pew Research Centre",
       color = NULL,
       fill = NULL) # removing legend title

g4
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-13-1.png" width="672" style="display: block; margin: auto;" />

## Custom Point Colour

To further replicate the original plot, we can also set colors and fill manually for points using `scale_color/fill_manual()`. Note that to do that, we need to specify a point shape that consist of area and border, such as shape 21. In the function, we can add values of the color/fill and labels for its respective color. We should also note that the order is based on the original level order in the data.


```r
g5 <- g4 + scale_color_manual(values = c("#DE8321","#1D84C6","#0B743D",
                                         "#000000", "#DA3411","#04452D"),
                              # we can provide custom label as well, respected to the data
                              labels = c("Asia &\nOceania", "Europe","Middle East &\nNorth Africa",
                                         "North\nAmerica", "South America", "Sub-Sahara Africa")
                              ) +
  scale_fill_manual(values = c("#EB983F","#3FCEEB","#3FB879",
                               "#E33010", "#FFFFFF","#0C8E5E"),
                    labels = c("Asia &\nOceania", "Europe","Middle East &\nNorth Africa",
                               "North\nAmerica", "South America", "Sub-Sahara Africa")
                    )

g5
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-14-1.png" width="672" style="display: block; margin: auto;" />

## Customize Theme

Next, we can tidy our plot using themes using the codes below. 


```r
g6 <- g5 + theme(

  # general
  text = element_text(family = "Segoe UI"),
  
  # legend
  legend.key.size = unit(0.1, "lines"), # set space between keys
  legend.key = element_rect(fill = NA), # removing backround of keys
  legend.text = element_text(size = 10),
  legend.position = "top",
  legend.direction = "horizontal",

  # axis
  axis.title = element_text(face = "italic", size = 10),
  
  # plot (all plot area including title, axis, etc.)
  plot.title = element_text(face = "bold", size = 15),
  plot.subtitle = element_text(size = 10, 
                               # adding some space between subtitle & legend
                               margin = margin(t =0,  
                                               r = 0, 
                                               b = 10, 
                                               l = 0, 
                                               unit = "pt")),
  plot.caption = element_text(size = 8, hjust = 0, vjust = 0.5),
  
  # panel (area where we map our geoms)
  panel.border = element_blank(),
  panel.background = element_blank(),
  panel.grid.major.x = element_blank(),
  panel.grid.major.y = element_line(size = 0.1, color = "grey66"),
  panel.grid.minor = element_blank()
  
  )

g6
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-15-1.png" width="672" style="display: block; margin: auto;" />

## Guides & Annotation

To have more flexibility in editing plot features such as legend, we can use `guides()` from ggplot2. There are various graphical features which you can apply guides into such as `guide_axis()`, `guide_colorbar()`, `guide_geoms()`, etc. We encourage you to visit the official ggplot2 documentation and explore it according to your needs on the net. Here, we will only use `guide_legend()` to custom our legend so that it will only display 1 row.


```r
g7 <- g6 + guides(color = guide_legend(nrow = 1))

g7
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-16-1.png" width="672" style="display: block; margin: auto;" />

Last but not least, we can add text annotation into our plot using the function `annotate()` from ggplot2. There are various geoms you can annotate inside a plot, such as a rectangle, an arrow, a segment, etc. For a friendly introduction, you can refer to [R Graph Gallery - Annotations](https://ggplot2.tidyverse.org/reference/annotate.html) or to the official [ggplot2 documentation](https://ggplot2.tidyverse.org/reference/annotate.html). 

Here, we will add annotations for the geometrical element `segment` and `text` for our trend line legend.


```r
g8 <- g7 + annotate(geom = "segment", # making a line segment
              x = 67, xend = 69, y = 13, yend = 13, # position of segment 
              colour = "#77200b", 
              size = 1
              ) +
     annotate(geom = "text", # making a text
              x = 70, y = 10, # position of text
              label = "Line of best fit,\ncorrelation coefficient = 87%",
              fontface = "italic", color = "#77200b", size = 4, family = "Segoe UI", 
              hjust = 0) # set the text on the right side of segment
```

# Final Plot

Now let's display our final plot! We can display our plot with the desired height and width by specifying it in the R chunk setting. Below is the plot created with an R Markdown chunk setting `fig.height = 6, fig.width = 7`.


```r
g8
```

<img src="/blog/2020-10-26-rplicate-series-i-can-t-get-no_files/figure-html/unnamed-chunk-18-1.png" width="672" style="display: block; margin: auto;" />

Thank you for reading and we hope this article can help you to create a more interesting visualization. We hope you can find the delight in exploring data visualization in R. Happy learning and coding!

