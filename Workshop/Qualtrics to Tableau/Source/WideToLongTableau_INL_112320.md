---
title: "WideToLongTableau_INL_112320"
author: "Min Hwangbo"
date: "11/23/2020"
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
library("tidyr")  # Data transformation package
library("readr")  # CSV loading package
```


# Step 2: Loading data

```r
data.wide <- read_csv("STARS_PLC_20-21.csv") # Loading data from an excel file
```

```
## Parsed with column specification:
## cols(
##   .default = col_character()
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
## # A tibble: 5 x 37
##   StartDate EndDate Status IPAddress Progress `Duration (in s… Finished
##   <chr>     <chr>   <chr>  <chr>     <chr>    <chr>            <chr>   
## 1 "Start D… "End D… "Resp… "IP Addr… "Progre… "Duration (in s… "Finish…
## 2 "{\"Impo… "{\"Im… "{\"I… "{\"Impo… "{\"Imp… "{\"ImportId\":… "{\"Imp…
## 3 "2020-11… "2020-… "IP A… "24.113.… "100"    "136"            "True"  
## 4 "2020-11… "2020-… "IP A… "71.236.… "100"    "163"            "True"  
## 5 "2020-11… "2020-… "IP A… "204.195… "100"    "357"            "True"  
## # … with 30 more variables: RecordedDate <chr>, ResponseId <chr>,
## #   RecipientLastName <chr>, RecipientFirstName <chr>, RecipientEmail <chr>,
## #   ExternalReference <chr>, LocationLatitude <chr>, LocationLongitude <chr>,
## #   DistributionChannel <chr>, UserLanguage <chr>, Q14 <chr>, Q15_1 <chr>,
## #   Q15_2 <chr>, Q15_3 <chr>, Q15_4 <chr>, Q15_5 <chr>, Q15_6 <chr>,
## #   Q15_7 <chr>, Q15_8 <chr>, Q15_9 <chr>, Q15_10 <chr>, Q15_11 <chr>,
## #   Q15_12 <chr>, Q15_13 <chr>, Q15_14 <chr>, Q15_15 <chr>, Q16 <chr>,
## #   Q17 <chr>, Q18 <chr>, Q19 <chr>
```

```r
tail(data.wide, 5) # Last five records
```

```
## # A tibble: 5 x 37
##   StartDate EndDate Status IPAddress Progress `Duration (in s… Finished
##   <chr>     <chr>   <chr>  <chr>     <chr>    <chr>            <chr>   
## 1 2020-11-… 2020-1… IP Ad… 68.116.1… 100      211              True    
## 2 2020-11-… 2020-1… IP Ad… 63.135.6… 100      273              True    
## 3 2020-11-… 2020-1… IP Ad… 73.37.10… 100      108              True    
## 4 2020-11-… 2020-1… IP Ad… 68.116.1… 100      232              True    
## 5 2020-11-… 2020-1… IP Ad… 174.204.… 100      463              True    
## # … with 30 more variables: RecordedDate <chr>, ResponseId <chr>,
## #   RecipientLastName <chr>, RecipientFirstName <chr>, RecipientEmail <chr>,
## #   ExternalReference <chr>, LocationLatitude <chr>, LocationLongitude <chr>,
## #   DistributionChannel <chr>, UserLanguage <chr>, Q14 <chr>, Q15_1 <chr>,
## #   Q15_2 <chr>, Q15_3 <chr>, Q15_4 <chr>, Q15_5 <chr>, Q15_6 <chr>,
## #   Q15_7 <chr>, Q15_8 <chr>, Q15_9 <chr>, Q15_10 <chr>, Q15_11 <chr>,
## #   Q15_12 <chr>, Q15_13 <chr>, Q15_14 <chr>, Q15_15 <chr>, Q16 <chr>,
## #   Q17 <chr>, Q18 <chr>, Q19 <chr>
```

```r
ls(data.wide)   # Vars names
```

```
##  [1] "DistributionChannel"   "Duration (in seconds)" "EndDate"              
##  [4] "ExternalReference"     "Finished"              "IPAddress"            
##  [7] "LocationLatitude"      "LocationLongitude"     "Progress"             
## [10] "Q14"                   "Q15_1"                 "Q15_10"               
## [13] "Q15_11"                "Q15_12"                "Q15_13"               
## [16] "Q15_14"                "Q15_15"                "Q15_2"                
## [19] "Q15_3"                 "Q15_4"                 "Q15_5"                
## [22] "Q15_6"                 "Q15_7"                 "Q15_8"                
## [25] "Q15_9"                 "Q16"                   "Q17"                  
## [28] "Q18"                   "Q19"                   "RecipientEmail"       
## [31] "RecipientFirstName"    "RecipientLastName"     "RecordedDate"         
## [34] "ResponseId"            "StartDate"             "Status"               
## [37] "UserLanguage"
```

```r
## Data summary: 25 records / 37 vars
```

# Step 3a: Changing var names
* First, we're going to copy information on the 2nd row to the 1st row as Var Names.
* Second, we're going to delete the duplicate record.
* Last, we will check the data quality/code accuracy.


```r
names(data.wide) <- data.wide[1,] # Copy 2nd row as var names
```

```
## Warning: The `value` argument of ``names<-`()` must be a character vector as of tibble 3.0.0.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.
```

```r
data.wide_1 <- data.wide[-c(1, 2),] # Delete duplicate rows (1st and 2nd)

head(data.wide_1, 5) # First 5 records
```

```
## # A tibble: 5 x 37
##   `Start Date` `End Date` `Response Type` `IP Address` Progress `Duration (in s…
##   <chr>        <chr>      <chr>           <chr>        <chr>    <chr>           
## 1 2020-11-13 … 2020-11-1… IP Address      24.113.190.… 100      136             
## 2 2020-11-13 … 2020-11-1… IP Address      71.236.249.… 100      163             
## 3 2020-11-13 … 2020-11-1… IP Address      204.195.114… 100      357             
## 4 2020-11-13 … 2020-11-1… IP Address      96.95.131.1… 100      304             
## 5 2020-11-13 … 2020-11-1… IP Address      69.55.221.1… 100      312             
## # … with 31 more variables: Finished <chr>, `Recorded Date` <chr>, `Response
## #   ID` <chr>, `Recipient Last Name` <chr>, `Recipient First Name` <chr>,
## #   `Recipient Email` <chr>, `External Data Reference` <chr>, `Location
## #   Latitude` <chr>, `Location Longitude` <chr>, `Distribution Channel` <chr>,
## #   `User Language` <chr>, `Which training did you participate in?` <chr>, `Q15
## #   - Content provided matched the training description.` <chr>, `Q15 - Content
## #   provided matched the core competency level indicated in the training
## #   description.` <chr>, `Q15 - Examples and illustrations used in the training
## #   were relevant to practice.` <chr>, `Q15 - Handouts were useful.` <chr>,
## #   `Q15 - Trainer was knowledgeable about the topic.` <chr>, `Q15 - Trainer
## #   was well prepared.` <chr>, `Q15 - Content and methods of instruction
## #   honored my learning style and culture.` <chr>, `Q15 - Trainer was able to
## #   present the material using alternative methods, when needed.` <chr>, `Q15 -
## #   Trainer clearly and completely addressed questions.` <chr>, `Q15 - Training
## #   facilities were conducive to learning.` <chr>, `Q15 - As a result of
## #   training, my knowledge about the topic is enhanced.` <chr>, `Q15 - As a
## #   result of training, I can think of way(s) to enhance my work with children
## #   and/or families.` <chr>, `Q15 - I can apply this information to the
## #   diversity of families I serve.` <chr>, `Q15 - I was invested in learning
## #   from this training.` <chr>, `Q15 - I would certainly recommend this
## #   training to my colleagues.` <chr>, `For future training, what topic(s) are
## #   you looking for (Select your top three choices) - Early Care & Education
## #   Core Comptency Areas:` <chr>, `For future training, what topic(s) are you
## #   looking for (Select your top three choices) - Child & Youth Development
## #   Competency Areas:` <chr>, `What parts of the training worked best for
## #   you?` <chr>, `What changes would you suggest to the trainer?` <chr>
```

```r
tail(data.wide_1, 5) # Last 5 records
```

```
## # A tibble: 5 x 37
##   `Start Date` `End Date` `Response Type` `IP Address` Progress `Duration (in s…
##   <chr>        <chr>      <chr>           <chr>        <chr>    <chr>           
## 1 2020-11-13 … 2020-11-1… IP Address      68.116.10.82 100      211             
## 2 2020-11-13 … 2020-11-1… IP Address      63.135.63.1… 100      273             
## 3 2020-11-13 … 2020-11-1… IP Address      73.37.101.1… 100      108             
## 4 2020-11-13 … 2020-11-1… IP Address      68.116.10.82 100      232             
## 5 2020-11-13 … 2020-11-1… IP Address      174.204.78.… 100      463             
## # … with 31 more variables: Finished <chr>, `Recorded Date` <chr>, `Response
## #   ID` <chr>, `Recipient Last Name` <chr>, `Recipient First Name` <chr>,
## #   `Recipient Email` <chr>, `External Data Reference` <chr>, `Location
## #   Latitude` <chr>, `Location Longitude` <chr>, `Distribution Channel` <chr>,
## #   `User Language` <chr>, `Which training did you participate in?` <chr>, `Q15
## #   - Content provided matched the training description.` <chr>, `Q15 - Content
## #   provided matched the core competency level indicated in the training
## #   description.` <chr>, `Q15 - Examples and illustrations used in the training
## #   were relevant to practice.` <chr>, `Q15 - Handouts were useful.` <chr>,
## #   `Q15 - Trainer was knowledgeable about the topic.` <chr>, `Q15 - Trainer
## #   was well prepared.` <chr>, `Q15 - Content and methods of instruction
## #   honored my learning style and culture.` <chr>, `Q15 - Trainer was able to
## #   present the material using alternative methods, when needed.` <chr>, `Q15 -
## #   Trainer clearly and completely addressed questions.` <chr>, `Q15 - Training
## #   facilities were conducive to learning.` <chr>, `Q15 - As a result of
## #   training, my knowledge about the topic is enhanced.` <chr>, `Q15 - As a
## #   result of training, I can think of way(s) to enhance my work with children
## #   and/or families.` <chr>, `Q15 - I can apply this information to the
## #   diversity of families I serve.` <chr>, `Q15 - I was invested in learning
## #   from this training.` <chr>, `Q15 - I would certainly recommend this
## #   training to my colleagues.` <chr>, `For future training, what topic(s) are
## #   you looking for (Select your top three choices) - Early Care & Education
## #   Core Comptency Areas:` <chr>, `For future training, what topic(s) are you
## #   looking for (Select your top three choices) - Child & Youth Development
## #   Competency Areas:` <chr>, `What parts of the training worked best for
## #   you?` <chr>, `What changes would you suggest to the trainer?` <chr>
```

```r
ls(data.wide_1) # Vars name
```

```
##  [1] "Distribution Channel"                                                                                                                 
##  [2] "Duration (in seconds)"                                                                                                                
##  [3] "End Date"                                                                                                                             
##  [4] "External Data Reference"                                                                                                              
##  [5] "Finished"                                                                                                                             
##  [6] "For future training, what topic(s) are you looking for (Select your top three choices) - Child & Youth Development Competency Areas:" 
##  [7] "For future training, what topic(s) are you looking for (Select your top three choices) - Early Care & Education Core Comptency Areas:"
##  [8] "IP Address"                                                                                                                           
##  [9] "Location Latitude"                                                                                                                    
## [10] "Location Longitude"                                                                                                                   
## [11] "Progress"                                                                                                                             
## [12] "Q15 - As a result of training, I can think of way(s) to enhance my work with children and/or families."                               
## [13] "Q15 - As a result of training, my knowledge about the topic is enhanced."                                                             
## [14] "Q15 - Content and methods of instruction honored my learning style and culture."                                                      
## [15] "Q15 - Content provided matched the core competency level indicated in the training description."                                      
## [16] "Q15 - Content provided matched the training description."                                                                             
## [17] "Q15 - Examples and illustrations used in the training were relevant to practice."                                                     
## [18] "Q15 - Handouts were useful."                                                                                                          
## [19] "Q15 - I can apply this information to the diversity of families I serve."                                                             
## [20] "Q15 - I was invested in learning from this training."                                                                                 
## [21] "Q15 - I would certainly recommend this training to my colleagues."                                                                    
## [22] "Q15 - Trainer clearly and completely addressed questions."                                                                            
## [23] "Q15 - Trainer was able to present the material using alternative methods, when needed."                                               
## [24] "Q15 - Trainer was knowledgeable about the topic."                                                                                     
## [25] "Q15 - Trainer was well prepared."                                                                                                     
## [26] "Q15 - Training facilities were conducive to learning."                                                                                
## [27] "Recipient Email"                                                                                                                      
## [28] "Recipient First Name"                                                                                                                 
## [29] "Recipient Last Name"                                                                                                                  
## [30] "Recorded Date"                                                                                                                        
## [31] "Response ID"                                                                                                                          
## [32] "Response Type"                                                                                                                        
## [33] "Start Date"                                                                                                                           
## [34] "User Language"                                                                                                                        
## [35] "What changes would you suggest to the trainer?"                                                                                       
## [36] "What parts of the training worked best for you?"                                                                                      
## [37] "Which training did you participate in?"
```

```r
# 23 obs & 37 vars
```

# Step 3b: Checking `Null` values

```r
# summary(data.wide) 
## Data summary: 23 records / 37 vars - check for null values
sum(is.na(data.wide_1$`Which training did you participate in?`))
```

```
## [1] 0
```

```r
# 0 NULL RECORDS
```

# Step 3c: Checking finished survey (`FILTER` responses that are considered `finished`)

```r
data.wide_2 <- data.wide_1 %>%
  filter(Finished == "True") # Watch out for "True" spelling

# Quality check 
head(data.wide_2, 5) # First five records
```

```
## # A tibble: 5 x 37
##   `Start Date` `End Date` `Response Type` `IP Address` Progress `Duration (in s…
##   <chr>        <chr>      <chr>           <chr>        <chr>    <chr>           
## 1 2020-11-13 … 2020-11-1… IP Address      24.113.190.… 100      136             
## 2 2020-11-13 … 2020-11-1… IP Address      71.236.249.… 100      163             
## 3 2020-11-13 … 2020-11-1… IP Address      204.195.114… 100      357             
## 4 2020-11-13 … 2020-11-1… IP Address      96.95.131.1… 100      304             
## 5 2020-11-13 … 2020-11-1… IP Address      69.55.221.1… 100      312             
## # … with 31 more variables: Finished <chr>, `Recorded Date` <chr>, `Response
## #   ID` <chr>, `Recipient Last Name` <chr>, `Recipient First Name` <chr>,
## #   `Recipient Email` <chr>, `External Data Reference` <chr>, `Location
## #   Latitude` <chr>, `Location Longitude` <chr>, `Distribution Channel` <chr>,
## #   `User Language` <chr>, `Which training did you participate in?` <chr>, `Q15
## #   - Content provided matched the training description.` <chr>, `Q15 - Content
## #   provided matched the core competency level indicated in the training
## #   description.` <chr>, `Q15 - Examples and illustrations used in the training
## #   were relevant to practice.` <chr>, `Q15 - Handouts were useful.` <chr>,
## #   `Q15 - Trainer was knowledgeable about the topic.` <chr>, `Q15 - Trainer
## #   was well prepared.` <chr>, `Q15 - Content and methods of instruction
## #   honored my learning style and culture.` <chr>, `Q15 - Trainer was able to
## #   present the material using alternative methods, when needed.` <chr>, `Q15 -
## #   Trainer clearly and completely addressed questions.` <chr>, `Q15 - Training
## #   facilities were conducive to learning.` <chr>, `Q15 - As a result of
## #   training, my knowledge about the topic is enhanced.` <chr>, `Q15 - As a
## #   result of training, I can think of way(s) to enhance my work with children
## #   and/or families.` <chr>, `Q15 - I can apply this information to the
## #   diversity of families I serve.` <chr>, `Q15 - I was invested in learning
## #   from this training.` <chr>, `Q15 - I would certainly recommend this
## #   training to my colleagues.` <chr>, `For future training, what topic(s) are
## #   you looking for (Select your top three choices) - Early Care & Education
## #   Core Comptency Areas:` <chr>, `For future training, what topic(s) are you
## #   looking for (Select your top three choices) - Child & Youth Development
## #   Competency Areas:` <chr>, `What parts of the training worked best for
## #   you?` <chr>, `What changes would you suggest to the trainer?` <chr>
```

```r
tail(data.wide_2, 5) # Last five records
```

```
## # A tibble: 5 x 37
##   `Start Date` `End Date` `Response Type` `IP Address` Progress `Duration (in s…
##   <chr>        <chr>      <chr>           <chr>        <chr>    <chr>           
## 1 2020-11-13 … 2020-11-1… IP Address      68.116.10.82 100      211             
## 2 2020-11-13 … 2020-11-1… IP Address      63.135.63.1… 100      273             
## 3 2020-11-13 … 2020-11-1… IP Address      73.37.101.1… 100      108             
## 4 2020-11-13 … 2020-11-1… IP Address      68.116.10.82 100      232             
## 5 2020-11-13 … 2020-11-1… IP Address      174.204.78.… 100      463             
## # … with 31 more variables: Finished <chr>, `Recorded Date` <chr>, `Response
## #   ID` <chr>, `Recipient Last Name` <chr>, `Recipient First Name` <chr>,
## #   `Recipient Email` <chr>, `External Data Reference` <chr>, `Location
## #   Latitude` <chr>, `Location Longitude` <chr>, `Distribution Channel` <chr>,
## #   `User Language` <chr>, `Which training did you participate in?` <chr>, `Q15
## #   - Content provided matched the training description.` <chr>, `Q15 - Content
## #   provided matched the core competency level indicated in the training
## #   description.` <chr>, `Q15 - Examples and illustrations used in the training
## #   were relevant to practice.` <chr>, `Q15 - Handouts were useful.` <chr>,
## #   `Q15 - Trainer was knowledgeable about the topic.` <chr>, `Q15 - Trainer
## #   was well prepared.` <chr>, `Q15 - Content and methods of instruction
## #   honored my learning style and culture.` <chr>, `Q15 - Trainer was able to
## #   present the material using alternative methods, when needed.` <chr>, `Q15 -
## #   Trainer clearly and completely addressed questions.` <chr>, `Q15 - Training
## #   facilities were conducive to learning.` <chr>, `Q15 - As a result of
## #   training, my knowledge about the topic is enhanced.` <chr>, `Q15 - As a
## #   result of training, I can think of way(s) to enhance my work with children
## #   and/or families.` <chr>, `Q15 - I can apply this information to the
## #   diversity of families I serve.` <chr>, `Q15 - I was invested in learning
## #   from this training.` <chr>, `Q15 - I would certainly recommend this
## #   training to my colleagues.` <chr>, `For future training, what topic(s) are
## #   you looking for (Select your top three choices) - Early Care & Education
## #   Core Comptency Areas:` <chr>, `For future training, what topic(s) are you
## #   looking for (Select your top three choices) - Child & Youth Development
## #   Competency Areas:` <chr>, `What parts of the training worked best for
## #   you?` <chr>, `What changes would you suggest to the trainer?` <chr>
```

```r
ls(data.wide_2)   # Vars names
```

```
##  [1] "Distribution Channel"                                                                                                                 
##  [2] "Duration (in seconds)"                                                                                                                
##  [3] "End Date"                                                                                                                             
##  [4] "External Data Reference"                                                                                                              
##  [5] "Finished"                                                                                                                             
##  [6] "For future training, what topic(s) are you looking for (Select your top three choices) - Child & Youth Development Competency Areas:" 
##  [7] "For future training, what topic(s) are you looking for (Select your top three choices) - Early Care & Education Core Comptency Areas:"
##  [8] "IP Address"                                                                                                                           
##  [9] "Location Latitude"                                                                                                                    
## [10] "Location Longitude"                                                                                                                   
## [11] "Progress"                                                                                                                             
## [12] "Q15 - As a result of training, I can think of way(s) to enhance my work with children and/or families."                               
## [13] "Q15 - As a result of training, my knowledge about the topic is enhanced."                                                             
## [14] "Q15 - Content and methods of instruction honored my learning style and culture."                                                      
## [15] "Q15 - Content provided matched the core competency level indicated in the training description."                                      
## [16] "Q15 - Content provided matched the training description."                                                                             
## [17] "Q15 - Examples and illustrations used in the training were relevant to practice."                                                     
## [18] "Q15 - Handouts were useful."                                                                                                          
## [19] "Q15 - I can apply this information to the diversity of families I serve."                                                             
## [20] "Q15 - I was invested in learning from this training."                                                                                 
## [21] "Q15 - I would certainly recommend this training to my colleagues."                                                                    
## [22] "Q15 - Trainer clearly and completely addressed questions."                                                                            
## [23] "Q15 - Trainer was able to present the material using alternative methods, when needed."                                               
## [24] "Q15 - Trainer was knowledgeable about the topic."                                                                                     
## [25] "Q15 - Trainer was well prepared."                                                                                                     
## [26] "Q15 - Training facilities were conducive to learning."                                                                                
## [27] "Recipient Email"                                                                                                                      
## [28] "Recipient First Name"                                                                                                                 
## [29] "Recipient Last Name"                                                                                                                  
## [30] "Recorded Date"                                                                                                                        
## [31] "Response ID"                                                                                                                          
## [32] "Response Type"                                                                                                                        
## [33] "Start Date"                                                                                                                           
## [34] "User Language"                                                                                                                        
## [35] "What changes would you suggest to the trainer?"                                                                                       
## [36] "What parts of the training worked best for you?"                                                                                      
## [37] "Which training did you participate in?"
```

```r
# ALl remains the same: 23 obs, 37 vars
```

# Step 4: Data reshape (Wide -> Long)
* Using `gather` function to consolidate questions into one column. 

```r
data.long <- gather(data.wide_2, question, response, "Q15 - Content provided matched the training description.":"What changes would you suggest to the trainer?", factor_key = T)

data.long # Check the data
```

```
## # A tibble: 437 x 20
##    `Start Date` `End Date` `Response Type` `IP Address` Progress
##    <chr>        <chr>      <chr>           <chr>        <chr>   
##  1 2020-11-13 … 2020-11-1… IP Address      24.113.190.… 100     
##  2 2020-11-13 … 2020-11-1… IP Address      71.236.249.… 100     
##  3 2020-11-13 … 2020-11-1… IP Address      204.195.114… 100     
##  4 2020-11-13 … 2020-11-1… IP Address      96.95.131.1… 100     
##  5 2020-11-13 … 2020-11-1… IP Address      69.55.221.1… 100     
##  6 2020-11-13 … 2020-11-1… IP Address      63.135.62.1… 100     
##  7 2020-11-13 … 2020-11-1… IP Address      169.204.239… 100     
##  8 2020-11-13 … 2020-11-1… IP Address      24.16.3.54   100     
##  9 2020-11-13 … 2020-11-1… IP Address      209.181.41.… 100     
## 10 2020-11-13 … 2020-11-1… IP Address      97.90.70.194 100     
## # … with 427 more rows, and 15 more variables: `Duration (in seconds)` <chr>,
## #   Finished <chr>, `Recorded Date` <chr>, `Response ID` <chr>, `Recipient Last
## #   Name` <chr>, `Recipient First Name` <chr>, `Recipient Email` <chr>,
## #   `External Data Reference` <chr>, `Location Latitude` <chr>, `Location
## #   Longitude` <chr>, `Distribution Channel` <chr>, `User Language` <chr>,
## #   `Which training did you participate in?` <chr>, question <fct>,
## #   response <chr>
```

```r
head(data.long, 5) # First five
```

```
## # A tibble: 5 x 20
##   `Start Date` `End Date` `Response Type` `IP Address` Progress `Duration (in s…
##   <chr>        <chr>      <chr>           <chr>        <chr>    <chr>           
## 1 2020-11-13 … 2020-11-1… IP Address      24.113.190.… 100      136             
## 2 2020-11-13 … 2020-11-1… IP Address      71.236.249.… 100      163             
## 3 2020-11-13 … 2020-11-1… IP Address      204.195.114… 100      357             
## 4 2020-11-13 … 2020-11-1… IP Address      96.95.131.1… 100      304             
## 5 2020-11-13 … 2020-11-1… IP Address      69.55.221.1… 100      312             
## # … with 14 more variables: Finished <chr>, `Recorded Date` <chr>, `Response
## #   ID` <chr>, `Recipient Last Name` <chr>, `Recipient First Name` <chr>,
## #   `Recipient Email` <chr>, `External Data Reference` <chr>, `Location
## #   Latitude` <chr>, `Location Longitude` <chr>, `Distribution Channel` <chr>,
## #   `User Language` <chr>, `Which training did you participate in?` <chr>,
## #   question <fct>, response <chr>
```

```r
tail(data.long, 5) # Last five
```

```
## # A tibble: 5 x 20
##   `Start Date` `End Date` `Response Type` `IP Address` Progress `Duration (in s…
##   <chr>        <chr>      <chr>           <chr>        <chr>    <chr>           
## 1 2020-11-13 … 2020-11-1… IP Address      68.116.10.82 100      211             
## 2 2020-11-13 … 2020-11-1… IP Address      63.135.63.1… 100      273             
## 3 2020-11-13 … 2020-11-1… IP Address      73.37.101.1… 100      108             
## 4 2020-11-13 … 2020-11-1… IP Address      68.116.10.82 100      232             
## 5 2020-11-13 … 2020-11-1… IP Address      174.204.78.… 100      463             
## # … with 14 more variables: Finished <chr>, `Recorded Date` <chr>, `Response
## #   ID` <chr>, `Recipient Last Name` <chr>, `Recipient First Name` <chr>,
## #   `Recipient Email` <chr>, `External Data Reference` <chr>, `Location
## #   Latitude` <chr>, `Location Longitude` <chr>, `Distribution Channel` <chr>,
## #   `User Language` <chr>, `Which training did you participate in?` <chr>,
## #   question <fct>, response <chr>
```

```r
ls(data.long)
```

```
##  [1] "Distribution Channel"                  
##  [2] "Duration (in seconds)"                 
##  [3] "End Date"                              
##  [4] "External Data Reference"               
##  [5] "Finished"                              
##  [6] "IP Address"                            
##  [7] "Location Latitude"                     
##  [8] "Location Longitude"                    
##  [9] "Progress"                              
## [10] "question"                              
## [11] "Recipient Email"                       
## [12] "Recipient First Name"                  
## [13] "Recipient Last Name"                   
## [14] "Recorded Date"                         
## [15] "response"                              
## [16] "Response ID"                           
## [17] "Response Type"                         
## [18] "Start Date"                            
## [19] "User Language"                         
## [20] "Which training did you participate in?"
```

# Step 5: Saving as a CSV file
* FileName_ProjectName_MMDDYY(DateCreated)

```r
# CSV
write.csv(data.long, "V2STARS_PLC_20-21.csv")
```
