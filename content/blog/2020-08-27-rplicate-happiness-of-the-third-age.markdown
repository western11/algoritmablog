---
title: 'Rplicate Series: Happiness of The Third Age'
author: Handoyo Sjarif
github: https://github.com/t3981-h
date: '2020-08-27'
slug: rplicate-happiness-of-the-third-age
categories:
  - R
tags:
  - Data Visualization
  - Rplicate
  - ggplot2
  - ggrepel
  - extrafont
  - ggthemes
  - gridExtra
description: ''
featured: ''
featuredalt: ''
featuredpath: ''
linktitle: ''
type: post
---



Have you ever wonder how far `ggplot2` and other packages in R can go for data visualization? Here, we are introducing the **Rplicate Series**: a series of articles for data visualization in R using ggplot2 and other packages, in aim to replicate **The Economist** plot. The Economist is an international weekly newspaper that focuses on current affairs, international business, politics, and technology. Along with its article, various graphs were displayed with visuals that is captivating to the eye. 

In this first article, we will demonstrate a way to replicate a plot titled **"Happiness of the third age"**. The original plot was published along with the article ["Suicides Among British Pensioners are Falling, 8th October 2018"](https://www.economist.com/graphic-detail/2018/10/08/suicides-among-british-pensioners-are-falling), as seen below.

<center> ![](/img/rplicate1/original.png){width="100%"} </center>

We will replicate the plot by combining 2 different plots made by ggplot2 package. We will also learn how to:

* customize y and x-axis scales
* make a repelled text inside a plot using ggrepel package
* apply fonts to plot using extrafont package
* apply The Economist theme on ggthemes package
* and combining plots using gridExtra package

Let's dive in below!

# Dataset

The dataset used was a yearly record of suicides from male and female British pensioners per age group.


```r
data.m <- read.csv("data_input/Suicide_Male.csv", header = TRUE)
data.f <- read.csv("data_input/Suicide_Female.csv", header = TRUE)
```

Let's get a glimpse of our data: 


```r
head(data.m)
```

```
#>   years X10.to.29 X30.to.44 X45.to.59 X60.to.74 X75.and.over
#> 1  1981       9.8      19.5      23.1      22.0         28.6
#> 2  1982       9.1      19.0      23.6      22.0         28.1
#> 3  1983       9.0      19.7      22.5      22.5         28.6
#> 4  1984       9.5      20.4      23.0      21.3         27.9
#> 5  1985      10.8      21.6      23.2      22.6         29.1
#> 6  1986      11.5      20.9      22.5      23.5         31.3
```


```r
head(data.f)
```

```
#>   years X10.to.29 X30.to.44 X45.to.59 X60.to.74 X75.and.over
#> 1  1981       3.0       8.5      15.0      16.2         13.6
#> 2  1982       3.2       8.4      13.9      15.4         14.2
#> 3  1983       2.9       8.0      12.8      14.8         13.4
#> 4  1984       2.7       7.7      13.1      14.4         13.5
#> 5  1985       2.7       7.5      13.7      15.9         13.5
#> 6  1986       3.2       7.0      11.3      13.8         13.3
```

# Making the Plot

On making the plot, we will be making a separate Male and Female plot and later combining it with the package **gridExtra**.

## Plotting Male Data

From the original plot, we can see that there are several lines with different colors drawn on the plot. On making such lines, the original `geom_line()` function from **ggplot2** receives only one variable to distinguish each line on the plot. An option is to use `pivot_longer()` from **tidyr** package to transform the structure of our data from wide to long format. 

Another simpler option is to **use multiple geom_line functions in our plot, layer-by-layer**. This is a very convenient option moreover if you would like to really customize between each line by color, line type, width, etc. For example, using this option, you can set a line type for one line and leaving it to default for another. In this case, we will set a different color for each line, and a bolder line for the age group above 59.


```r
library(ggplot2)

# basic male plot
male <- ggplot(data = data.m, 
       mapping = aes(x=years)) + 
  geom_line(aes(y=X10.to.29), size=1.5, color="#6ecff6") + 
  geom_line(aes(y=X30.to.44), size=1.5, color="#a6a8aa") +
  geom_line(aes(y=X45.to.59), size=1.5, color="#6fbcc0") + 
  geom_line(aes(y=X60.to.74), size=2.2, color="#f05a40") + 
  geom_line(aes(y=X75.and.over), size=2.2, color="#7a2712") 

male
```

<img src="/blog/2020-08-27-rplicate-happiness-of-the-third-age_files/figure-html/unnamed-chunk-4-1.png" width="672" style="display: block; margin: auto;" />

Another tricky part here is to add text to our plot. We will be using `geom_text()` and customize its coordinates for each line. Notice that the coordinates were drawn from the estimated location from the plot. For example, the text for the line of age group 10-29 is approximately located in the 4th index of our data (the year 1984). From there, we can subset only that row of data and specify the value of x and y, and then increase/decrease its x and y value with `nudge_*`until we find the exact position. The subsetting step here is needed so our `geom_text()` function only makes one text for each line (not making for all 37 observations of our data). For the age group 45-59, we will also use the `geom_text_repel()` from **ggrepel** to make the text a little bit far from the actual line and draw a special point as mark.

Additionally, we will also import our locale font library to R so that we can apply it for our `geom_text()`. We will need the additional package **extrafont**. Notice that the text fonts need to be customized manually inside its geom because `theme()` only applies to the non-data part of the plot.


```r
library(extrafont)

# importing fonts to R
# font_import() 

# load all fonts so it can be used
loadfonts(device = "win") 
```


```r
library(ggrepel) # extra library for text repel for 45-59 age group 

# adding text
male1 <- male +
  geom_text(data=data.m[4,],
            aes(x=years, y=X10.to.29),
            label="10-29", color="#3c3c3c", nudge_y=-1.5, 
            fontface = "bold", family = "Segoe UI") + 
  geom_text(data=data.m[19,],
            aes(x=years, y=X30.to.44),
            label="30-44", color="#3c3c3c", nudge_y=1.5, 
            fontface = "bold", family = "Segoe UI") +
  geom_text_repel(data=data.m[33,],
                  aes(x=years, y=X45.to.59),
                  label="45-59", color="#3c3c3c", nudge_y = 3, 
                  fontface = "bold", family = "Segoe UI") +
  geom_point(data=data.m[33,],
             aes(y=X45.to.59)) +
  geom_text(data=data.m[7,],
            aes(x=years, y=X60.to.74),
            label="60-74", color="#f05a40", nudge_y = -3, nudge_x = -1.5, 
            fontface = "bold", family = "Segoe UI") +
  geom_text(data=data.m[10,],
            aes(y=X75.and.over, x=years),
            label="75+", color="#7a2712", nudge_y = 6, nudge_x = 1, 
            fontface = "bold", family = "Segoe UI")

male1
```

<img src="/blog/2020-08-27-rplicate-happiness-of-the-third-age_files/figure-html/unnamed-chunk-6-1.png" width="672" style="display: block; margin: auto;" />

Next, let's customize scales.


```r
male2 <- male1 + 
  scale_x_continuous(breaks = seq(1981,2017,5)) + # sequence from 1981 to 2017, by 5
  scale_y_continuous(position= "right", 
                     limits= c(0,35), 
                     breaks = seq(0,35,5),
                     expand = expansion(add = c(0, 2))) # removing spaces on the min-max y-axis
  
male2
```

<img src="/blog/2020-08-27-rplicate-happiness-of-the-third-age_files/figure-html/unnamed-chunk-7-1.png" width="672" style="display: block; margin: auto;" />

Lastly, let's add labels and beautify our plot with themes. Luckily, there is already the `theme_economist_white()` from the `ggthemes` package.


```r
library(ggthemes)

male3 <- male2 + 
  labs(title = "Happiness of the third age", 
       subtitle = "Britain, suicides per 100,000 people, by age group \n \n Male",
       caption = "\n Source: ONS") + # \n for breaks (space between axis text and caption)
  theme_economist_white(gray_bg = FALSE) +
  theme(axis.title.x=element_blank(), # removing axis title
        axis.title.y=element_blank(),
        axis.text.y.right  = element_text(margin = margin(t =0,b=0,r=0,l=10)), # adjusting margin
        text = element_text(family = "Segoe UI", size = 11),
        plot.caption=element_text(hjust = 0)) 

male3
```

<img src="/blog/2020-08-27-rplicate-happiness-of-the-third-age_files/figure-html/unnamed-chunk-8-1.png" width="672" style="display: block; margin: auto;" />

Whew! That was a lot of meticulous work! Now, making a female plot shouldn't be too hard.

## Plotting Female Data


```r
female <- ggplot(data = data.f, 
                 mapping = aes(x=years)) + 
  geom_line(aes(y=X10.to.29), size=1.5, color="#6ecff6") + 
  geom_line(aes(y=X30.to.44), size=1.5, color="#a6a8aa") +
  geom_line(aes(y=X45.to.59), size=1.5, color="#6fbcc0") + 
  geom_line(aes(y=X60.to.74), size=2.2, color="#f05a40") + 
  geom_line(aes(y=X75.and.over), size=2.2, color="#7a2712") +
  labs(title = "",
       subtitle = "\n \n Female",
       caption = "\n ") + 
  scale_x_continuous(breaks = seq(1981,2017,5)) +
  scale_y_continuous(position= "right", 
                     limits= c(0,35), 
                     breaks = seq(0,35,5),
                     expand = expansion(add = c(0, 2))) +
  theme_economist_white(gray_bg = FALSE) +
  theme(axis.title.x=element_blank(),
        axis.title.y=element_blank(),
        axis.text.y.right  = element_text(margin = margin(t =0,b=0,r=0,l=10)),
        text = element_text(family = "Segoe UI", size = 11))

female
```

<img src="/blog/2020-08-27-rplicate-happiness-of-the-third-age_files/figure-html/unnamed-chunk-9-1.png" width="672" style="display: block; margin: auto;" />

## Combining Female and Male Plot

Below we can combine the male and female plot using `grid.arrange()` from gridExtra.


```r
library(gridExtra)

grid.arrange(male3, female, nrow=1)
```

<img src="/blog/2020-08-27-rplicate-happiness-of-the-third-age_files/figure-html/unnamed-chunk-10-1.png" width="816" style="display: block; margin: auto;" />

Thank you for reading and we hope this article can help you to find the delight in exploring data visualization in R. Happy learning and coding!

