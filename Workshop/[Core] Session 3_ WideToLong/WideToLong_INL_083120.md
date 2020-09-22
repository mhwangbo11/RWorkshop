---
title: "WideToLong_INL_083120"
author: "Min Hwangbo"
date: "8/31/2020"
output:
  html_document:
    preserve_yaml: true
    toc: true
    toc_float: true
    keep_md: true
published: false
---



# Step 1: Loading packages - Data reshape

```r
# Install packages first using install.packages("pacakgename") on your console!
## Reference: http://www.cookbook-r.com/Manipulating_data/Converting_data_between_wide_and_long_format/ 
### Inspiration: Dr. Liz Sanders's HLM class
#### Inspiration II: Chuck Lanfear Intro to R: https://clanfear.github.io/CSSS508/

library("dplyr")  # Data reshaping package
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
library("tidyr")  # Data transformation package
library("readr")  # CSV loading package
```


# Step 2: Loading data

```r
data.wide <- read_csv("EvalSTARS_CPD_051320.csv") # Loading data from an excel file
```

```
## Parsed with column specification:
## cols(
##   .default = col_character(),
##   Finished = col_logical()
## )
```

```
## See spec(...) for full column specifications.
```

```r
# Quality check descriptive
head(data.wide, 5) # First five records
```

```
## # A tibble: 5 x 22
##   Finished `Recorded Date` `Which training~ `Please mark on~ `Please mark on~
##   <lgl>    <chr>           <chr>            <chr>            <chr>           
## 1 FALSE    3/24/20 9:49    <NA>             <NA>             <NA>            
## 2 FALSE    3/26/20 9:52    <NA>             <NA>             <NA>            
## 3 FALSE    3/26/20 12:00   <NA>             <NA>             <NA>            
## 4 FALSE    3/26/20 13:18   <NA>             <NA>             <NA>            
## 5 FALSE    3/26/20 14:17   <NA>             <NA>             <NA>            
## # ... with 17 more variables: `Please mark only one response per line. -
## #   Examples and illustrations used in the training were relevant to
## #   practice.` <chr>, `Please mark only one response per line. - Handouts were
## #   useful.` <chr>, `Please mark only one response per line. - Trainer was
## #   knowledgeable about the topic.` <chr>, `Please mark only one response per
## #   line. - Trainer was well prepared.` <chr>, `Please mark only one response
## #   per line. - Content and methods of instruction honored my learning style
## #   and culture.` <chr>, `Please mark only one response per line. - Trainer was
## #   able to present the material using alternative methods, when
## #   needed.` <chr>, `Please mark only one response per line. - Trainer clearly
## #   and completely addressed questions.` <chr>, `Please mark only one response
## #   per line. - Training facilities were conducive to learning.` <chr>, `Please
## #   mark only one response per line. - As a result of training, my knowledge
## #   about the topic is enhanced.` <chr>, `Please mark only one response per
## #   line. - As a result of training, I can think of way(s) to enhance my work
## #   with children and/or families.` <chr>, `Please mark only one response per
## #   line. - I can apply this information to the diversity of families I
## #   serve.` <chr>, `Please mark only one response per line. - I was invested in
## #   learning from this training.` <chr>, `Please mark only one response per
## #   line. - I would certainly recommend this training to my colleagues.` <chr>,
## #   `What parts of the training worked best for you?` <chr>, `What changes
## #   would you suggest to the trainer?` <chr>, `For future training, what
## #   topic(s) are you looking for (Select your top three choices) - Early Care &
## #   Education Core Comptency Areas:` <chr>, `For future training, what topic(s)
## #   are you looking for (Select your top three choices) - Child & Youth
## #   Development Competency Areas:` <chr>
```

```r
tail(data.wide, 5) # Last five records
```

```
## # A tibble: 5 x 22
##   Finished `Recorded Date` `Which training~ `Please mark on~ `Please mark on~
##   <lgl>    <chr>           <chr>            <chr>            <chr>           
## 1 TRUE     4/10/20 15:11   ITERS-3 Trainin~ Agree            Agree           
## 2 TRUE     4/10/20 15:12   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## 3 TRUE     4/10/20 15:12   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## 4 TRUE     4/10/20 15:13   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## 5 TRUE     4/10/20 15:14   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## # ... with 17 more variables: `Please mark only one response per line. -
## #   Examples and illustrations used in the training were relevant to
## #   practice.` <chr>, `Please mark only one response per line. - Handouts were
## #   useful.` <chr>, `Please mark only one response per line. - Trainer was
## #   knowledgeable about the topic.` <chr>, `Please mark only one response per
## #   line. - Trainer was well prepared.` <chr>, `Please mark only one response
## #   per line. - Content and methods of instruction honored my learning style
## #   and culture.` <chr>, `Please mark only one response per line. - Trainer was
## #   able to present the material using alternative methods, when
## #   needed.` <chr>, `Please mark only one response per line. - Trainer clearly
## #   and completely addressed questions.` <chr>, `Please mark only one response
## #   per line. - Training facilities were conducive to learning.` <chr>, `Please
## #   mark only one response per line. - As a result of training, my knowledge
## #   about the topic is enhanced.` <chr>, `Please mark only one response per
## #   line. - As a result of training, I can think of way(s) to enhance my work
## #   with children and/or families.` <chr>, `Please mark only one response per
## #   line. - I can apply this information to the diversity of families I
## #   serve.` <chr>, `Please mark only one response per line. - I was invested in
## #   learning from this training.` <chr>, `Please mark only one response per
## #   line. - I would certainly recommend this training to my colleagues.` <chr>,
## #   `What parts of the training worked best for you?` <chr>, `What changes
## #   would you suggest to the trainer?` <chr>, `For future training, what
## #   topic(s) are you looking for (Select your top three choices) - Early Care &
## #   Education Core Comptency Areas:` <chr>, `For future training, what topic(s)
## #   are you looking for (Select your top three choices) - Child & Youth
## #   Development Competency Areas:` <chr>
```

```r
ls(data.wide)   # Vars names
```

```
##  [1] "Finished"                                                                                                                                  
##  [2] "For future training, what topic(s) are you looking for (Select your top three choices) - Child & Youth Development Competency Areas:"      
##  [3] "For future training, what topic(s) are you looking for (Select your top three choices) - Early Care & Education Core Comptency Areas:"     
##  [4] "Please mark only one response per line. - As a result of training, I can think of way(s) to enhance my work with children and/or families."
##  [5] "Please mark only one response per line. - As a result of training, my knowledge about the topic is enhanced."                              
##  [6] "Please mark only one response per line. - Content and methods of instruction honored my learning style and culture."                       
##  [7] "Please mark only one response per line. - Content provided matched the core competency level indicated in the training description."       
##  [8] "Please mark only one response per line. - Content provided matched the training description."                                              
##  [9] "Please mark only one response per line. - Examples and illustrations used in the training were relevant to practice."                      
## [10] "Please mark only one response per line. - Handouts were useful."                                                                           
## [11] "Please mark only one response per line. - I can apply this information to the diversity of families I serve."                              
## [12] "Please mark only one response per line. - I was invested in learning from this training."                                                  
## [13] "Please mark only one response per line. - I would certainly recommend this training to my colleagues."                                     
## [14] "Please mark only one response per line. - Trainer clearly and completely addressed questions."                                             
## [15] "Please mark only one response per line. - Trainer was able to present the material using alternative methods, when needed."                
## [16] "Please mark only one response per line. - Trainer was knowledgeable about the topic."                                                      
## [17] "Please mark only one response per line. - Trainer was well prepared."                                                                      
## [18] "Please mark only one response per line. - Training facilities were conducive to learning."                                                 
## [19] "Recorded Date"                                                                                                                             
## [20] "What changes would you suggest to the trainer?"                                                                                            
## [21] "What parts of the training worked best for you?"                                                                                           
## [22] "Which training did you participate in?"
```

```r
## Data summary: 112 records / 22 vars - check for null values
```

# Step 3: Checking `Null` values

```r
# summary(data.wide) 
## Data summary: 112 records / 22 vars - check for null values
sum(is.na(data.wide$`Which training did you participate in?`))
```

```
## [1] 26
```

```r
# 26 records = also happens to be something called "False" from the data set.
```

# Step 4: Data reshape (`Filter` responses that are considered `finished`)

```r
data.wide2 <- data.wide %>%
  filter(Finished == "TRUE")

# Quality check 
head(data.wide2, 5) # First five records
```

```
## # A tibble: 5 x 22
##   Finished `Recorded Date` `Which training~ `Please mark on~ `Please mark on~
##   <lgl>    <chr>           <chr>            <chr>            <chr>           
## 1 TRUE     4/1/20 17:17    <NA>             Strongly agree   Strongly agree  
## 2 TRUE     4/23/20 17:33   <NA>             <NA>             <NA>            
## 3 TRUE     5/7/20 14:02    Coach Framework~ Strongly agree   Strongly agree  
## 4 TRUE     5/7/20 14:04    Coach Framework~ Strongly agree   Agree           
## 5 TRUE     5/7/20 14:05    Coach Framework~ Strongly disagr~ Strongly disagr~
## # ... with 17 more variables: `Please mark only one response per line. -
## #   Examples and illustrations used in the training were relevant to
## #   practice.` <chr>, `Please mark only one response per line. - Handouts were
## #   useful.` <chr>, `Please mark only one response per line. - Trainer was
## #   knowledgeable about the topic.` <chr>, `Please mark only one response per
## #   line. - Trainer was well prepared.` <chr>, `Please mark only one response
## #   per line. - Content and methods of instruction honored my learning style
## #   and culture.` <chr>, `Please mark only one response per line. - Trainer was
## #   able to present the material using alternative methods, when
## #   needed.` <chr>, `Please mark only one response per line. - Trainer clearly
## #   and completely addressed questions.` <chr>, `Please mark only one response
## #   per line. - Training facilities were conducive to learning.` <chr>, `Please
## #   mark only one response per line. - As a result of training, my knowledge
## #   about the topic is enhanced.` <chr>, `Please mark only one response per
## #   line. - As a result of training, I can think of way(s) to enhance my work
## #   with children and/or families.` <chr>, `Please mark only one response per
## #   line. - I can apply this information to the diversity of families I
## #   serve.` <chr>, `Please mark only one response per line. - I was invested in
## #   learning from this training.` <chr>, `Please mark only one response per
## #   line. - I would certainly recommend this training to my colleagues.` <chr>,
## #   `What parts of the training worked best for you?` <chr>, `What changes
## #   would you suggest to the trainer?` <chr>, `For future training, what
## #   topic(s) are you looking for (Select your top three choices) - Early Care &
## #   Education Core Comptency Areas:` <chr>, `For future training, what topic(s)
## #   are you looking for (Select your top three choices) - Child & Youth
## #   Development Competency Areas:` <chr>
```

```r
tail(data.wide2, 5) # Last five records
```

```
## # A tibble: 5 x 22
##   Finished `Recorded Date` `Which training~ `Please mark on~ `Please mark on~
##   <lgl>    <chr>           <chr>            <chr>            <chr>           
## 1 TRUE     4/10/20 15:11   ITERS-3 Trainin~ Agree            Agree           
## 2 TRUE     4/10/20 15:12   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## 3 TRUE     4/10/20 15:12   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## 4 TRUE     4/10/20 15:13   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## 5 TRUE     4/10/20 15:14   ITERS-3 Trainin~ Strongly agree   Strongly agree  
## # ... with 17 more variables: `Please mark only one response per line. -
## #   Examples and illustrations used in the training were relevant to
## #   practice.` <chr>, `Please mark only one response per line. - Handouts were
## #   useful.` <chr>, `Please mark only one response per line. - Trainer was
## #   knowledgeable about the topic.` <chr>, `Please mark only one response per
## #   line. - Trainer was well prepared.` <chr>, `Please mark only one response
## #   per line. - Content and methods of instruction honored my learning style
## #   and culture.` <chr>, `Please mark only one response per line. - Trainer was
## #   able to present the material using alternative methods, when
## #   needed.` <chr>, `Please mark only one response per line. - Trainer clearly
## #   and completely addressed questions.` <chr>, `Please mark only one response
## #   per line. - Training facilities were conducive to learning.` <chr>, `Please
## #   mark only one response per line. - As a result of training, my knowledge
## #   about the topic is enhanced.` <chr>, `Please mark only one response per
## #   line. - As a result of training, I can think of way(s) to enhance my work
## #   with children and/or families.` <chr>, `Please mark only one response per
## #   line. - I can apply this information to the diversity of families I
## #   serve.` <chr>, `Please mark only one response per line. - I was invested in
## #   learning from this training.` <chr>, `Please mark only one response per
## #   line. - I would certainly recommend this training to my colleagues.` <chr>,
## #   `What parts of the training worked best for you?` <chr>, `What changes
## #   would you suggest to the trainer?` <chr>, `For future training, what
## #   topic(s) are you looking for (Select your top three choices) - Early Care &
## #   Education Core Comptency Areas:` <chr>, `For future training, what topic(s)
## #   are you looking for (Select your top three choices) - Child & Youth
## #   Development Competency Areas:` <chr>
```

```r
ls(data.wide2)   # Vars names
```

```
##  [1] "Finished"                                                                                                                                  
##  [2] "For future training, what topic(s) are you looking for (Select your top three choices) - Child & Youth Development Competency Areas:"      
##  [3] "For future training, what topic(s) are you looking for (Select your top three choices) - Early Care & Education Core Comptency Areas:"     
##  [4] "Please mark only one response per line. - As a result of training, I can think of way(s) to enhance my work with children and/or families."
##  [5] "Please mark only one response per line. - As a result of training, my knowledge about the topic is enhanced."                              
##  [6] "Please mark only one response per line. - Content and methods of instruction honored my learning style and culture."                       
##  [7] "Please mark only one response per line. - Content provided matched the core competency level indicated in the training description."       
##  [8] "Please mark only one response per line. - Content provided matched the training description."                                              
##  [9] "Please mark only one response per line. - Examples and illustrations used in the training were relevant to practice."                      
## [10] "Please mark only one response per line. - Handouts were useful."                                                                           
## [11] "Please mark only one response per line. - I can apply this information to the diversity of families I serve."                              
## [12] "Please mark only one response per line. - I was invested in learning from this training."                                                  
## [13] "Please mark only one response per line. - I would certainly recommend this training to my colleagues."                                     
## [14] "Please mark only one response per line. - Trainer clearly and completely addressed questions."                                             
## [15] "Please mark only one response per line. - Trainer was able to present the material using alternative methods, when needed."                
## [16] "Please mark only one response per line. - Trainer was knowledgeable about the topic."                                                      
## [17] "Please mark only one response per line. - Trainer was well prepared."                                                                      
## [18] "Please mark only one response per line. - Training facilities were conducive to learning."                                                 
## [19] "Recorded Date"                                                                                                                             
## [20] "What changes would you suggest to the trainer?"                                                                                            
## [21] "What parts of the training worked best for you?"                                                                                           
## [22] "Which training did you participate in?"
```

# Step 5: Data reshape (Wide -> Long)
* Using `gather` function to consolidate questions into one column. 

```r
data.long <- gather(data.wide2, question, response, "Please mark only one response per line. - Content provided matched the training description.":"For future training, what topic(s) are you looking for (Select your top three choices) - Child & Youth Development Competency Areas:", factor_key = T)

data.long # Check the data
```

```
## # A tibble: 1,596 x 5
##    Finished `Recorded Date` `Which training did ~ question             response 
##    <lgl>    <chr>           <chr>                 <fct>                <chr>    
##  1 TRUE     4/1/20 17:17    <NA>                  Please mark only on~ Strongly~
##  2 TRUE     4/23/20 17:33   <NA>                  Please mark only on~ <NA>     
##  3 TRUE     5/7/20 14:02    Coach Framework Trai~ Please mark only on~ Strongly~
##  4 TRUE     5/7/20 14:04    Coach Framework Trai~ Please mark only on~ Strongly~
##  5 TRUE     5/7/20 14:05    Coach Framework Trai~ Please mark only on~ Strongly~
##  6 TRUE     5/7/20 14:07    Coach Framework Trai~ Please mark only on~ Strongly~
##  7 TRUE     5/7/20 14:09    Coach Framework Trai~ Please mark only on~ Agree    
##  8 TRUE     5/7/20 14:09    Coach Framework Trai~ Please mark only on~ Strongly~
##  9 TRUE     5/7/20 14:09    Coach Framework Trai~ Please mark only on~ Agree    
## 10 TRUE     5/7/20 14:10    Coach Framework Trai~ Please mark only on~ Strongly~
## # ... with 1,586 more rows
```

```r
head(data.long, 5) # First five
```

```
## # A tibble: 5 x 5
##   Finished `Recorded Date` `Which training did ~ question              response 
##   <lgl>    <chr>           <chr>                 <fct>                 <chr>    
## 1 TRUE     4/1/20 17:17    <NA>                  Please mark only one~ Strongly~
## 2 TRUE     4/23/20 17:33   <NA>                  Please mark only one~ <NA>     
## 3 TRUE     5/7/20 14:02    Coach Framework Trai~ Please mark only one~ Strongly~
## 4 TRUE     5/7/20 14:04    Coach Framework Trai~ Please mark only one~ Strongly~
## 5 TRUE     5/7/20 14:05    Coach Framework Trai~ Please mark only one~ Strongly~
```

```r
tail(data.long, 5) # Last five
```

```
## # A tibble: 5 x 5
##   Finished `Recorded Date` `Which training di~ question         response        
##   <lgl>    <chr>           <chr>               <fct>            <chr>           
## 1 TRUE     4/10/20 15:11   ITERS-3 Training: ~ For future trai~ Learning Enviro~
## 2 TRUE     4/10/20 15:12   ITERS-3 Training: ~ For future trai~ Learning Enviro~
## 3 TRUE     4/10/20 15:12   ITERS-3 Training: ~ For future trai~ Families, Commu~
## 4 TRUE     4/10/20 15:13   ITERS-3 Training: ~ For future trai~ Learning Enviro~
## 5 TRUE     4/10/20 15:14   ITERS-3 Training: ~ For future trai~ Interactions wi~
```

# Step 6: Saving as a CSV file
* FileName_ProjectName_MMDDYY(DateCreated)

```r
# CSV
write.csv(data.long, "V2STARS_CPD_051320")
```
