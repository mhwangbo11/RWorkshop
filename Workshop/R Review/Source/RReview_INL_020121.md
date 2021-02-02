---
title: "RReview_INL_020121"
author: "Min Hwangbo"
date: "2/1/2021"
output: 
  html_document:
    preserve_yaml: true
    toc: true
    toc_float: true
    keep_md: true
published: false
---



* Inspiration: [本橋智光, 2019](http://www.yes24.com/24/AuthorFile/Author/278360)
* RMD reference: [Hwangbo, 2020](https://github.com/mhwangbo11/Workshop/blob/master/Workshop/Data%20Transformation/Source/DataReshape_INL_071320.rmd)

# Step 1: Loading packages

```r
# Install your packages first by install.packages("packagename") on your console!

library("dplyr") # Data reshaping package
library("tidyr") # Data reshaping package
library("readr") # Reading a csv file
```

# Step 2: Loading data set


```r
cleandata <- read_csv("~/Desktop/UWCL/2020-21/Innovation Lab/Data Viz/Case2_DCYF/Data/ENG_DCYFCQI_020121.csv")
```

```
## 
## ── Column specification ────────────────────────────────────────────────────────
## cols(
##   Type = col_character(),
##   Language = col_character(),
##   Question = col_character(),
##   Question_Desc = col_character(),
##   Response = col_character()
## )
```

```r
# Obs:772, Var: 5 
## Snapshot - run: View(cleandata) OR summary(cleandata) on your console

### Quality Check
head(cleandata, 5) # Clear
```

```
## # A tibble: 5 x 5
##   Type  Language Question Question_Desc                 Response                
##   <chr> <chr>    <chr>    <chr>                         <chr>                   
## 1 Coach English  Q5       What type of support do you … Engaging providers in u…
## 2 Coach English  Q5       What type of support do you … Getting providers to “ …
## 3 Coach English  Q5       What type of support do you … language and translatio…
## 4 Coach English  Q5       What type of support do you … all of the above        
## 5 Coach English  Q5       What type of support do you … I am really going to ne…
```

```r
tail(cleandata, 5) # Clear
```

```
## # A tibble: 5 x 5
##   Type   Language Question Question_Desc               Response                 
##   <chr>  <chr>    <chr>    <chr>                       <chr>                    
## 1 Provi… English  Q13      Are there any other questi… "What standardized tools…
## 2 Provi… English  Q13      Are there any other questi… "No"                     
## 3 Provi… English  Q13      Are there any other questi… "I think EA needs to rel…
## 4 Provi… English  Q13      Are there any other questi… "This is a very short ti…
## 5 Provi… English  Q13      Are there any other questi… "It's still not clear to…
```

```r
ls(cleandata) # Good to know
```

```
## [1] "Language"      "Question"      "Question_Desc" "Response"     
## [5] "Type"
```

# Step 3: `Filter null` na, invalid data
* We're going to use a piper (` %>% `), then use a `filter` function.

## Step 3a: Null/NA all those stuff

```r
# Make sure to do a quality check after each round
## Creating brothers and sisters of "na"'s first

nas <- c("na", "Na", "n/a", "N/a", "NA", "?", "-", "N/A", "X", "x", "", " ")
data1 <- cleandata %>% 
  filter(!Response %in% nas) %>% # Filtering the `nas` data frame
    filter(!is.na(Response))       # Filtering na responses which is different than above

### Final Obs: 740, Var: 5
```

## Step 3b: If needed, "No" records

```r
# DIY
```

# Step 4: Save as a CSV `write.csv`
* FileName_ProjectName_MMDDYY(DateCreated)

```r
write.csv(data1, "CleanENG_DCYFCQI_020121.csv")

# Make sure to check your final csv data. 
## R will likely generate id on the first column.
```

