# Homework-1
---
title: "Homework Assignment 1"
author: "Yusuf Uzhunnan"
date: 'Assigned: Oct 24, 2020, Due Sun Nov 01, 2020 11:59PM'
output:
  html_document:
    toc: yes
  pdf_document:
    toc: yes
---

## HW-1 Instructions

> Our class emphasizes clear communication of data analysis results to non-technical audiences. I expect your HTML output documents to be readable and well formatted. I expect you to put ample comments in your R code to make the code understandable. Along with accuracy of results, I am looking for quality of presentation as well. This homework is due by **11:59PM on Nov 01st**.  To complete this assignment, follow these steps:
1. Create a new RStudio Project for this HW. Sync the project directory with a GitHub Repository (see instructions on Canvas on how to do that). 

2. Download the `HW1.Rmd` file from Canvas. Save the file in the RStudio Project Directory. 

3. Open `HW1.Rmd` in RStudio. Replace the "Your Name Here" text in the `author:` field with your name.

4. Supply your solutions to the homework by editing `HW1.Rmd`.

5. Run your code in the Console and Knit HTML frequently to check for errors.

6. You may find it easier to solve a problem by interacting only with the Console at first. 

7. When you have completed the homework please check that your knits correctly when you click `Knit HTML`.

8. "Push" all your local changes to the GitHub Repo for the project.

9. Submit your RMD file, the HTML output and a link to your GitHub Repo on Canvas.


## Data frame basics

We will continue working with the nycflights dataset we looked at in class. **Please be sure to keep the data file in the same directory as the RMD file - your project directory.**

First - make sure that you are reading the data correctly and doing all the data cleaning steps that we did in class. Feel free to go beyond our class work in cleaning the data if you feel the need. 

I now would like you to answer the following - all in their own separate R code blocks.

### Import Data

First thing we will do is import data

```{r}
# Using read.csv for reading the csv file
nyc <- read.csv("nycflights.csv")
```

### Clean up Data

```{r, cache=TRUE}
# Delete the column named "X"
nyc$X <- NULL
# Recode columns as factors as needed
nyc$carrier <- as.factor(nyc$carrier)
nyc$flight <- as.factor(nyc$flight)
nyc$tailnum <- as.factor(nyc$tailnum)
nyc$origin <- as.factor(nyc$origin)
nyc$dest <- as.factor(nyc$dest)
# Check Summary
summary(nyc)
```

### Data Exploration

Let's first do some simple exploration of this data. I would like for you to answer each of the questions below with an **inline R code** integrated into the text and not in a separate R code block.

- How many airlines are there? (Hint: `levels` and `length` can be useful here)

```{r}
numAirlines <- length(levels(nyc$carrier))
```

  There are `r numAirlines` airlines.

- How many flights there were by the airline with code `OO`? (Hint: `nrow` can be useful here along with logical indexing)

```{r}
flightsByOO <- nrow(nyc[nyc$carrier == "OO", ])
```

  There were `r flightsByOO` flights by the airline with code 'OO'.

- How long is the shortest flight out of any NYC airport? (Hint: `min` can be useful, remember to handle `NA` values)

```{r}
shortestFlightTime <- min(nyc$air_time, na.rm = TRUE)
```
  The shortest flight out of any NYC airport is `r shortestFlightTime` minutes.

- How many flights where there by United Airlines (code: UA) on Jan 12th 2013?

```{r}
flightsByUAJan122013 <- nrow(nyc[nyc$carrier == "UA" & nyc$year == 2013 & nyc$month == 1 & nyc$day == 12, ])
```
  On January 12th, 2013, there were `r flightsByUAJan122013` flights by United Airlines


### Arrival Delay

Lets focus on Arrival Delay.

- What was the average arrival delay for all airports and all airlines combined in Jan 2013? 

```{r}
arrDelaysJan2013 <- nyc[nyc$year == 2013 & nyc$month == 1, ]
mean(arrDelaysJan2013$arr_delay, na.rm = TRUE)
```
  
  The average arrival delay for all airports and all airlines combined in Jan 2013 was `r mean(arrDelaysJan2013$arr_delay, na.rm = TRUE)` minutes.

- Whats was the median arrival delay for all airports and all airlines combined in Jan 2013?

```{r}
median(arrDelaysJan2013$arr_delay, na.rm = TRUE)
```

  The median arrival delay for all airports and all airlines combined in Jan 2013 was `r median(arrDelaysJan2013$arr_delay, na.rm = TRUE)` minutes.

Based on your answers to the two questions above, what can you say about the distribution of arrival delays? Provide your answer in a text paragraph form.

Based on the previous answers, I can say that the distribution of arrival delays in January 2013 is skewed right or positively skewed. This is because the mean of the arrival times is greater than the median of the arrival times. This tells us there are more arrival delays that are lower than the mean than there are arrival times higher than the mean.


### Airline Performance

Lets see if all airlines are equally terrible as far as flight arrival delays are concerned. For this question you will have to make sure that airline column is coded as a factor.

- Calculate average arrival delays by airline (Hint: look up the command `tapply`)

```{r}
avgArrDelayByAirline <- round(tapply(nyc$arr_delay, nyc$carrier, mean, na.rm = TRUE),2)
```

The average arrival delays by airline are: `r avgArrDelayByAirline` for each airline from 9E to YV respectively.

- Draw a Bar Plot of Average Arrival Delays for all the Airlines (Hint: command for making a Bar Plot is simply `barplot`)

```{r}
barplot(sort(avgArrDelayByAirline, decreasing = TRUE))
```

- Which airline has the highest average arrival delay? Which airline has the smallest average arrival delay? Are there airlines that actually have negative average delay? Provide answer to this question in a text paragraph form using **inline R code**.

```{r}
maxDelayAirline <- names(avgArrDelayByAirline[avgArrDelayByAirline == max(avgArrDelayByAirline)])
minDelayAirline <- names(avgArrDelayByAirline[avgArrDelayByAirline == min(avgArrDelayByAirline)])
negativeDelayAirlines <- names(avgArrDelayByAirline[avgArrDelayByAirline <= 0])
```


The airline with the highest average arrival delay is `r maxDelayAirline`. The airline with the lowest average arrival delay is `r minDelayAirline`. The airlines with a negative average delay time are `r negativeDelayAirlines[1]` and `r negativeDelayAirlines[2]`.


### Air Gain

Create a new column named airgain such that airgain = (departure delay - arrival delay) : this is the amount of delay a flight made up while in air.

```{r}
nyc["airgain"] <- nyc$dep_delay - nyc$arr_delay
```


a) Explore airgain data - calculate suitable descriptive statistics and appropriate graphics to better understand this data. This part is open ended - you do what you feel works best for you.

```{r}
# Mean airgain
mean(nyc$airgain, na.rm = TRUE)
# Median airgain
median(nyc$airgain, na.rm = TRUE)
# Max airgain
max(nyc$airgain, na.rm = TRUE)
# Min airgain
min(nyc$airgain, na.rm = TRUE)
#Overall Summary
summary(nyc$airgain)
```


b) Answer the questions:

  - do airlines actually gain any time when in air on average? 
  
  Yes, airlines actually do gain an average of `r round(mean(nyc$airgain, na.rm = TRUE), 2)` minutes when in air.

  - Calculate average airgain for different airlines - which airlines do a better job, which do a worse job? 
  
```{r}
# Average airgain by airline
avgAirgainByAirline <- round(tapply(nyc$airgain, nyc$carrier, mean, na.rm = TRUE),2)
# Sorted highest to lowest
sortedAvgAirgains <- sort(avgAirgainByAirline, decreasing = TRUE)
bestAirlinesByAirgain <- names(sortedAvgAirgains[1:3])
worstAirlinesByAirgain <- names(sortedAvgAirgains[14:16])
sortedAvgAirgains
```

The three airlines doing the best job with airgain are `r bestAirlinesByAirgain[1]`, `r bestAirlinesByAirgain[2]`, and `r bestAirlinesByAirgain[3]`.
The three airlines doing the worst with airgain and actually losing time in the air are `r worstAirlinesByAirgain[1]`, `r worstAirlinesByAirgain[2]`, and `r worstAirlinesByAirgain[3]`.

  - Build a graphic  of average airgain for different airlines sorted by airgain. We want to graphically visualize which airlines are good at "catching up" when in air and which airlines actually lose time further after taking off.
  
```{r}
# Bar graph of average airgain by airline, sorted
barplot(sort(avgAirgainByAirline, decreasing = TRUE))
```
  

That's it. Once you are done, make sure everything works and knits well and then you can push your changes to the GitHub repo and uplaod the RMD flile and the html output to Canvas.

**Have Fun!**

Sanjeev
© 2020 GitHub, Inc.
Terms
Privacy
Security
Status
Help
Contact GitHub
Pricing
API
Training
Blog
About
