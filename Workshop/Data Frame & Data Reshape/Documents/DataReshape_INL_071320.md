---
title: "DataReshape_INL_071320"
author: "Min Hwangbo"
date: "7/13/2020"
output:
  html_document:
    preserve_yaml: true
    toc: true
    toc_float: true
    keep_md: true
published: false
---



* Inspiration: [本橋智光, 2019](http://www.yes24.com/24/AuthorFile/Author/278360)
* Data Source: [ECEAP Sites](https://data.wa.gov/Education/DCYF-ECEAP-Sites/8ydb-ddzd) @ Data.WA.GOV

# Step 1: Load packages

```r
# Install packages first using install.packages("pacakgename") on your console!

library("dplyr") # Data reshaping package
library("tidyr") # Data reshaping package
library("readr") # Reading a csv file
```

# Step 2: Preparation

## Load your file & Quality Check
Recommended to load `csv` file when possible using `readr` package and `read_csv` function.


```r
Original <- read_csv("ECEAPSites_DCYF_070120.csv")
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
# Original file

summary(Original, 5) # Summarize what this file is about
```

```
##       Q1                 Q2                 Q3                 Q4           
##  Length:391         Length:391         Length:391         Length:391        
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##       Q5                 Q6                 Q7                 Q8           
##  Length:391         Length:391         Length:391         Length:391        
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##       Q9                Q10                Q11                Q12           
##  Length:391         Length:391         Length:391         Length:391        
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##      Q13                Q14                Q15                Q16           
##  Length:391         Length:391         Length:391         Length:391        
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##      Q17                Q18                Q19                Q20           
##  Length:391         Length:391         Length:391         Length:391        
##  Class :character   Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
##      Q21                Q22                Q23           
##  Length:391         Length:391         Length:391        
##  Class :character   Class :character   Class :character  
##  Mode  :character   Mode  :character   Mode  :character
```

```r
head(Original, 5) # First 5 records
```

```
## # A tibble: 5 x 23
##   Q1     Q2    Q3    Q4    Q5    Q6    Q7    Q8    Q9    Q10   Q11   Q12   Q13  
##   <chr>  <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
## 1 SiteO… Site… Alte… SubC… ECEA… Cont… ECEA… Faci… Line… City  State Zip … Coun…
## 2 2,039  <NA>  <NA>  2038  <NA>  2,028 Earl… Fami… <NA>  <NA>  WA    99207 Spok…
## 3 1,443  Birc… Birc… <NA>  Dire… 30    Oppo… Non-… 3328… Bell… WA    98225 What…
## 4 303    Orov… Orov… <NA>  Dire… 27    Okan… Head… 1114… Orov… WA    98844 Okan…
## 5 2,320  Chil… <NA>  2303  Chil… 36    Snoh… Non-… 14 E… Ever… WA    98208 Snoh…
## # … with 10 more variables: Q14 <chr>, Q15 <chr>, Q16 <chr>, Q17 <chr>,
## #   Q18 <chr>, Q19 <chr>, Q20 <chr>, Q21 <chr>, Q22 <chr>, Q23 <chr>
```

```r
tail(Original, 5) # Last 5 records
```

```
## # A tibble: 5 x 23
##   Q1    Q2     Q3    Q4    Q5    Q6    Q7    Q8    Q9    Q10   Q11   Q12   Q13  
##   <chr> <chr>  <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr> <chr>
## 1 2,183 McGil… <NA>  <NA>  Dire… 10    Educ… Head… 2401… Vanc… WA    98683 Clark
## 2 1,958 Minne… <NA>  <NA>  Dire… 14    ESD … Non-… 4517… Vanc… WA    98661 Clark
## 3 2,356 Walla… <NA>  <NA>  Dire… 1,364 ESD … Publ… 1150… Wall… WA    99362 Wall…
## 4 2,262 Newpo… <NA>  <NA>  Dire… 1,964 Bell… Publ… 5225… Bell… WA    98006 King 
## 5 679   Ellsw… <NA>  <NA>  Dire… 10    Educ… Head… 512 … VANC… WA    98664 Clark
## # … with 10 more variables: Q14 <chr>, Q15 <chr>, Q16 <chr>, Q17 <chr>,
## #   Q18 <chr>, Q19 <chr>, Q20 <chr>, Q21 <chr>, Q22 <chr>, Q23 <chr>
```

```r
ls(Original) # Vars name
```

```
##  [1] "Q1"  "Q10" "Q11" "Q12" "Q13" "Q14" "Q15" "Q16" "Q17" "Q18" "Q19" "Q2" 
## [13] "Q20" "Q21" "Q22" "Q23" "Q3"  "Q4"  "Q5"  "Q6"  "Q7"  "Q8"  "Q9"
```

```r
# 391 obs & 23 vars?
## But if you carefully check the original data from the website, it has the name of variables: Try View(Original) on your console
```

## Change Var Names

* First, we're going to copy information on the 2nd row to the 1st row as Var Names.
* Second, we're going to delete the duplicate record.
* Last, we will check the data quality/code accuracy.


```r
names(Original) <- Original[1,] # Copy 2nd row as var names
```

```
## Warning: The `value` argument of ``names<-`()` must be a character vector as of tibble 3.0.0.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_warnings()` to see where this warning was generated.
```

```r
Revision <- Original[-1,] # Delete duplicate (1st) row

head(Revision, 5) # First 5 records
```

```
## # A tibble: 5 x 23
##   SiteOrganizatio… `Site Name` `Alternate Name… SubContractorOr…
##   <chr>            <chr>       <chr>            <chr>           
## 1 2,039            <NA>        <NA>             2038            
## 2 1,443            Birches     Birches ECEAP    <NA>            
## 3 303              Oroville E… Oroville ECEAP   <NA>            
## 4 2,320            ChildStrive <NA>             2303            
## 5 387              Wapato Chi… <NA>             <NA>            
## # … with 19 more variables: `ECEAP Subcontractor Name` <chr>,
## #   ContractorOrganizationId <chr>, `ECEAP Contractor Name` <chr>, `Facility
## #   Type` <chr>, `Line 1 Address` <chr>, City <chr>, State <chr>, `Zip
## #   Code` <chr>, County <chr>, `Enrollment Phone Number` <chr>, `Emergency
## #   Contact Name` <chr>, `Emergency Contact Email` <chr>, `Emergency Contact
## #   Phone` <chr>, `Alternate Phone` <chr>, `Total Funded Slots` <chr>, `Minimum
## #   Age of ECEAP Children in Months` <chr>, `Maximum Age of ECEAP Children in
## #   Months` <chr>, GeoCodedPhysicalAddress <chr>, EAParticipation <chr>
```

```r
tail(Revision, 5) # Last 5 records
```

```
## # A tibble: 5 x 23
##   SiteOrganizatio… `Site Name` `Alternate Name… SubContractorOr…
##   <chr>            <chr>       <chr>            <chr>           
## 1 2,183            McGillivray <NA>             <NA>            
## 2 1,958            Minnehaha … <NA>             <NA>            
## 3 2,356            Walla Wall… <NA>             <NA>            
## 4 2,262            Newport He… <NA>             <NA>            
## 5 679              Ellsworth   <NA>             <NA>            
## # … with 19 more variables: `ECEAP Subcontractor Name` <chr>,
## #   ContractorOrganizationId <chr>, `ECEAP Contractor Name` <chr>, `Facility
## #   Type` <chr>, `Line 1 Address` <chr>, City <chr>, State <chr>, `Zip
## #   Code` <chr>, County <chr>, `Enrollment Phone Number` <chr>, `Emergency
## #   Contact Name` <chr>, `Emergency Contact Email` <chr>, `Emergency Contact
## #   Phone` <chr>, `Alternate Phone` <chr>, `Total Funded Slots` <chr>, `Minimum
## #   Age of ECEAP Children in Months` <chr>, `Maximum Age of ECEAP Children in
## #   Months` <chr>, GeoCodedPhysicalAddress <chr>, EAParticipation <chr>
```

```r
ls(Revision) # Vars name
```

```
##  [1] "Alternate Name for the Site"            
##  [2] "Alternate Phone"                        
##  [3] "City"                                   
##  [4] "ContractorOrganizationId"               
##  [5] "County"                                 
##  [6] "EAParticipation"                        
##  [7] "ECEAP Contractor Name"                  
##  [8] "ECEAP Subcontractor Name"               
##  [9] "Emergency Contact Email"                
## [10] "Emergency Contact Name"                 
## [11] "Emergency Contact Phone"                
## [12] "Enrollment Phone Number"                
## [13] "Facility Type"                          
## [14] "GeoCodedPhysicalAddress"                
## [15] "Line 1 Address"                         
## [16] "Maximum Age of ECEAP Children in Months"
## [17] "Minimum Age of ECEAP Children in Months"
## [18] "Site Name"                              
## [19] "SiteOrganizationId"                     
## [20] "State"                                  
## [21] "SubContractorOrganizationId"            
## [22] "Total Funded Slots"                     
## [23] "Zip Code"
```

```r
# 390 obs & 23 vars
```

# Step 3: Data Extract

## Extract Certain Variables
What if we're only interested in extracting certain variables such as:

* Emergency Contact Name
* Site Name
* City
* Zip Code
* EAParticipation

Let's use `select` function!

```r
ECEAP1 <- Revision %>% 
  select("Emergency Contact Name", "Site Name", "City", "State", "Zip Code", "EAParticipation") %>% 
as.data.frame()
# 390 obs & 6 vars
```

Then... you probably remember we should at least check:

* First five records
* Last five records
* Vars name


```r
head(ECEAP1, 5) # First 5 records
```

```
##   Emergency Contact Name                       Site Name       City State
## 1                   <NA>                            <NA>       <NA>    WA
## 2           Tami Thomsen                         Birches Bellingham    WA
## 3            Tauni Allen                  Oroville ECEAP   Oroville    WA
## 4            Deb Walrath                     ChildStrive    Everett    WA
## 5         Toni Gutierrez Wapato Child Development Center     Wapato    WA
##   Zip Code EAParticipation
## 1    99207             Yes
## 2    98225             Yes
## 3    98844             Yes
## 4    98208             Yes
## 5    98951             Yes
```

```r
tail(ECEAP1, 5) # Last 5 records
```

```
##     Emergency Contact Name                       Site Name        City State
## 386              Dana Taub                     McGillivray   Vancouver    WA
## 387         Lisa MacDonald Minnehaha Early Learning Center   Vancouver    WA
## 388          Jenyfer Reese     Walla Walla ECEAP Blueridge Walla Walla    WA
## 389       Jennifer Sanislo  Newport Heights Early Learning    Bellevue    WA
## 390         Diana Atkinson                       Ellsworth   VANCOUVER    WA
##     Zip Code EAParticipation
## 386    98683             Yes
## 387    98661             Yes
## 388    99362             Yes
## 389    98006             Yes
## 390    98664             Yes
```

```r
ls(ECEAP1, 5)
```

```
## [1] "City"                   "EAParticipation"        "Emergency Contact Name"
## [4] "Site Name"              "State"                  "Zip Code"
```

```r
# Looks good!
```

## Renaming variable name
* Some of the variable names are way too long or have spaces in between.
* Let's change those names using `rename` function!
* "NewName" = `CurrentVarName`

```r
ECEAP2 <- ECEAP1 %>% rename("EATF" = `EAParticipation`, "EmergencyContact" = `Emergency Contact Name`, "SiteName" = `Site Name`, "ZipCode" = `Zip Code`) # For renaming dataframe column
# Don't forget to conduct your quality check!

head(ECEAP2, 5) # First 5 records
```

```
##   EmergencyContact                        SiteName       City State ZipCode
## 1             <NA>                            <NA>       <NA>    WA   99207
## 2     Tami Thomsen                         Birches Bellingham    WA   98225
## 3      Tauni Allen                  Oroville ECEAP   Oroville    WA   98844
## 4      Deb Walrath                     ChildStrive    Everett    WA   98208
## 5   Toni Gutierrez Wapato Child Development Center     Wapato    WA   98951
##   EATF
## 1  Yes
## 2  Yes
## 3  Yes
## 4  Yes
## 5  Yes
```

```r
tail(ECEAP2, 5) # Last 5 records
```

```
##     EmergencyContact                        SiteName        City State ZipCode
## 386        Dana Taub                     McGillivray   Vancouver    WA   98683
## 387   Lisa MacDonald Minnehaha Early Learning Center   Vancouver    WA   98661
## 388    Jenyfer Reese     Walla Walla ECEAP Blueridge Walla Walla    WA   99362
## 389 Jennifer Sanislo  Newport Heights Early Learning    Bellevue    WA   98006
## 390   Diana Atkinson                       Ellsworth   VANCOUVER    WA   98664
##     EATF
## 386  Yes
## 387  Yes
## 388  Yes
## 389  Yes
## 390  Yes
```

```r
ls(ECEAP2, 5) # Vars name
```

```
## [1] "City"             "EATF"             "EmergencyContact" "SiteName"        
## [5] "State"            "ZipCode"
```

```r
# Clear!
```

## Extract based on condition: `EATF = T/F`
* What if we're only interested in looking at ECEAP sites who are participating in Early Achievers?
* Let's use `filter` function!

```r
ECEAP3 <- ECEAP2 %>% 
  filter(EATF == "Yes")

# Time for quality check!
## View data set on your console! View(ECEAP3)
head(ECEAP3,5) # First five records
```

```
##   EmergencyContact                        SiteName       City State ZipCode
## 1             <NA>                            <NA>       <NA>    WA   99207
## 2     Tami Thomsen                         Birches Bellingham    WA   98225
## 3      Tauni Allen                  Oroville ECEAP   Oroville    WA   98844
## 4      Deb Walrath                     ChildStrive    Everett    WA   98208
## 5   Toni Gutierrez Wapato Child Development Center     Wapato    WA   98951
##   EATF
## 1  Yes
## 2  Yes
## 3  Yes
## 4  Yes
## 5  Yes
```

```r
tail(ECEAP3,5) # Last five records
```

```
##     EmergencyContact                        SiteName        City State ZipCode
## 371        Dana Taub                     McGillivray   Vancouver    WA   98683
## 372   Lisa MacDonald Minnehaha Early Learning Center   Vancouver    WA   98661
## 373    Jenyfer Reese     Walla Walla ECEAP Blueridge Walla Walla    WA   99362
## 374 Jennifer Sanislo  Newport Heights Early Learning    Bellevue    WA   98006
## 375   Diana Atkinson                       Ellsworth   VANCOUVER    WA   98664
##     EATF
## 371  Yes
## 372  Yes
## 373  Yes
## 374  Yes
## 375  Yes
```

```r
ls(ECEAP3) # Vars name
```

```
## [1] "City"             "EATF"             "EmergencyContact" "SiteName"        
## [5] "State"            "ZipCode"
```

```r
# Should have equal or smaller obs!
## 375 obs & 6 vars
### Clear!
```

# Step 4: Split data: `Extract`
Let's split name from `EmergencyContact` to 1) FirstName and 2) LastName

```r
ECEAP4 <- extract(ECEAP3, "EmergencyContact", c("FirstName", "LastName"), "([^ ]+) (.*)") # Still can't figure out this code - Retrieved from Stackflow

# You'll notice on the Environment that now we have 7 vars!
head(ECEAP4, 5)
```

```
##   FirstName  LastName                        SiteName       City State ZipCode
## 1      <NA>      <NA>                            <NA>       <NA>    WA   99207
## 2      Tami   Thomsen                         Birches Bellingham    WA   98225
## 3     Tauni     Allen                  Oroville ECEAP   Oroville    WA   98844
## 4       Deb   Walrath                     ChildStrive    Everett    WA   98208
## 5      Toni Gutierrez Wapato Child Development Center     Wapato    WA   98951
##   EATF
## 1  Yes
## 2  Yes
## 3  Yes
## 4  Yes
## 5  Yes
```

```r
tail(ECEAP4, 5)
```

```
##     FirstName  LastName                        SiteName        City State
## 371      Dana      Taub                     McGillivray   Vancouver    WA
## 372      Lisa MacDonald Minnehaha Early Learning Center   Vancouver    WA
## 373   Jenyfer     Reese     Walla Walla ECEAP Blueridge Walla Walla    WA
## 374  Jennifer   Sanislo  Newport Heights Early Learning    Bellevue    WA
## 375     Diana  Atkinson                       Ellsworth   VANCOUVER    WA
##     ZipCode EATF
## 371   98683  Yes
## 372   98661  Yes
## 373   99362  Yes
## 374   98006  Yes
## 375   98664  Yes
```

```r
ls(ECEAP4)
```

```
## [1] "City"      "EATF"      "FirstName" "LastName"  "SiteName"  "State"    
## [7] "ZipCode"
```

And we probably won't need TF var anymore for the final version if we think about adopting these codes for other project (i.e. CTM) 

```r
Final <- ECEAP4 %>% 
  select("FirstName", "LastName", "City", "State", "ZipCode") %>% 
as.data.frame()
# Should have 5 vars

head(Final, 5)
```

```
##   FirstName  LastName       City State ZipCode
## 1      <NA>      <NA>       <NA>    WA   99207
## 2      Tami   Thomsen Bellingham    WA   98225
## 3     Tauni     Allen   Oroville    WA   98844
## 4       Deb   Walrath    Everett    WA   98208
## 5      Toni Gutierrez     Wapato    WA   98951
```

```r
tail(Final, 5)
```

```
##     FirstName  LastName        City State ZipCode
## 371      Dana      Taub   Vancouver    WA   98683
## 372      Lisa MacDonald   Vancouver    WA   98661
## 373   Jenyfer     Reese Walla Walla    WA   99362
## 374  Jennifer   Sanislo    Bellevue    WA   98006
## 375     Diana  Atkinson   VANCOUVER    WA   98664
```

```r
ls(Final)
```

```
## [1] "City"      "FirstName" "LastName"  "State"     "ZipCode"
```

# Step 5: Save as a CSV `write.csv`
* FileName_ProjectName_MMDDYY(DateCreated)

```r
write.csv(Final, "EASites_ECEAP_071220")
# Make sure to check your final csv data. 
## R will likely generate id on the first column.
```
