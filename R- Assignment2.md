---
title: "R-Assignment2"
author: "Payton Kim"
date: "9/19/2019"
output: rmarkdown::github_document
  #pdf_document: default
  #'html_document: default': default
 # html_document: default
---

1. Download the c2015 dataset to your computer. Use function getwd() to check the current working
directory. Use setwd() to change the current directory to the c2015 file

```{r}
getwd()
setwd("C:/Users/student/Downloads")
```
2. 2. We need to install a package to read the xlsx file. (Let’s not change the xlsx to csv here) There are a few packages for this. I recommend to use the readxl package. This package is contained in the
tidyverse package so if you already installed tidyverse, you should have it already. If not, install and load the readxl package by
```{r}
library(readxl)
```
3. Use read_excel() to read the c2015 dataset. Use function class() to check the type of data you just
read in. You will notice that the data now is not just a data frame, it is also a tibble. A tibble is
a generalization of a data frame, so you can still use all the functions and syntax for data frame with
tibble.

```{r}
c <- read_excel("C:/Users/student/Downloads/c2015.xlsx")
class(c)
```
4. Use dim function to check the dimension of the data. Since this data is quite big, a common practice
is to randomly subset the data to analyze. Use sample function to create a new dataset that has
a random 1000 observations from the original data. Use set.seed(2019) before using the sample
function to set the seed for the randomness so that everyone in class is working with the same random
subset of the data.

```{r}
dim(c)
set.seed(2019)
samplec <- c[sample(nrow(c),1000),]
```
5. Use summary function to have a quick look at the data. You will notice there is one variable is actually a constant. Remove that variable from the data.

```{r}
summary(samplec)
library(dplyr)
c2015 = select(samplec, -"YEAR")
summary(c2015)
```
6. Check the number of missing values (NA) in each column.

```{r}
sum(is.na(c2015))
sum(is.na(c2015[,1]))
sum(is.na(c2015[,2]))
sum(is.na(c2015[,3]))
sum(is.na(c2015[,4]))
sum(is.na(c2015[,5]))
sum(is.na(c2015[,6]))
sum(is.na(c2015[,7]))
sum(is.na(c2015[,8]))
sum(is.na(c2015[,9]))
sum(is.na(c2015[,10]))
sum(is.na(c2015[,11]))
sum(is.na(c2015[,12]))
sum(is.na(c2015[,13]))
sum(is.na(c2015[,14]))
sum(is.na(c2015[,15]))
sum(is.na(c2015[,16]))
sum(is.na(c2015[,17]))
sum(is.na(c2015[,18]))
sum(is.na(c2015[,19]))
sum(is.na(c2015[,20]))
sum(is.na(c2015[,21]))
sum(is.na(c2015[,22]))
sum(is.na(c2015[,23]))
sum(is.na(c2015[,24]))
sum(is.na(c2015[,25]))
sum(is.na(c2015[,26]))
sum(is.na(c2015[,27]))
```
7. There are missing values in this data that are not NAs. Identify the form of these missing values.
Check the number of these missing values in each column. Notice that you may want to use na.rm =
TRUE when counting these missing values.

```{r}
colSums(c2015 == "Unknown")
```
8. Change the missing values in SEX variable to “Female”

```{r}
c2015$SEX <- ifelse(c2015$SEX == "Unknown","Female",c2015$SEX)

colSums(c2015 == "Unknown")
```
9. Fix the AGE variable so that it is in the right form and has no missing values. Hint:
• Change the value Less than 1 to 0 (string 0, not a number 0)
• Change the type of the variable to numeric using as.numeric function
• Change the missing values to the average of the age.

```{r}
c2015$AGE <- ifelse(c2015$AGE == "Less than 1","0", c2015$AGE)
c2015$AGE <- as.numeric(c2015$AGE)
mean <- mean(c2015$AGE,na.rm = TRUE)
c2015$AGE <- ifelse(is.na(c2015$AGE),mean, c2015$AGE)
```
10. Put the TRAV_SP(Travel Speed) variable in the right form (type) and remove all missing values. Calculate the average speed. You can use a non-base R function for this question. Hint: check out the
function str_replace

```{r}
library(stringr)
noMPH <- str_replace(c2015$TRAV_SP, "MPH","")
noMPHnumeric <- as.numeric(noMPH)
c2015$TRAV_SP <- noMPHnumeric
new2015 <- na.omit(c2015)
travsp <- na.omit(noMPHnumeric)
mean(travsp)
```
11. Compare the average speed of those who had "No Apprent Injury" and the rest. What do you
observe?

```{r}
mean(new2015$TRAV_SP[new2015$INJ_SEV == "No Apparent Injury (O)"])
mean(new2015$TRAV_SP[new2015$INJ_SEV != "No Apparent Injury (O)"])
###Those with no apparent injury were driving, on average, slower than those with injuries
```
12. Use the SEAT_POS variable to filter the data so that there is only drivers in the dataset. Compare the average speed of man drivers and woman drivers. Comment on the results.

```{r}
question12 <- new2015%>%
  filter(new2015$SEAT_POS == "Front Seat, Left Side")
man <- mean(question12$TRAV_SP[question12$SEX == "Male"])
woman <- mean(question12$TRAV_SP[question12$SEX == "Female"])
man
woman
###Women were, on average, driving about 6 MPH slower than men were if they got into a car accident
```
13. Compare the average speed of drivers who drink and those who do not. Comment on the results.
Hint: This calculation can be done manually or by using the aggregate function or by function in
base R.

```{r}
drink <- mean(question12$TRAV_SP[question12$DRINKING == "Yes (Alcohol Involved)"])
nodrink <- mean(question12$TRAV_SP[question12$DRINKING != "Yes (Alcohol Involved)"])

drink
nodrink
###The drivers in which alcohol was inolver drove, on average, over 20MPH more than drivers who did not have alcohol involved in their accident
```
14. Hypothesize about the age range of drivers who may drive more aggressively. Test your hypothesis by
comparing the average speed of those in this age range and the rest. Comment on the results.

```{r}
###I would hypothesize that drivers less than age 30 drive more aggressively/faster than drivers who are at or over the age of 30.
lessthan30 <- mean(question12$TRAV_SP[question12$AGE < 30])
thirtyandup <- mean(question12$TRAV_SP[question12$AGE >= 30])

lessthan30
thirtyandup

###Drivers who were less than the age of thirty were driving on average 54MPH which is about 6MPH more than drivers who were at or over the age of 30.
```
15. If the data did not confirm your hypothesis in 14. Could you identify an age group of drivers who may drive more aggressively?

```{r}
###The data in my hypothesis from question 14 was correct.
```


