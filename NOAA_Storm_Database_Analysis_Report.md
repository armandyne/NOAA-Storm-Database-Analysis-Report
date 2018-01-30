---
title: Exploring the NOAA Storm Database
author: Arman Iskaliyev
date: 30 January 2018
output: 
  html_document:
    keep_md: true
---

------------------------------------------------------------------------

#Consequences of severe weather events in the U.S. between 1950 and 2011

------------------------------------------------------------------------

###Synopsis
___________
In this report bla bla....


###Data Processing
__________________
We obtained our [data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2) from the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States which occurred in the period between 1950 and November of 2011. Information in database also including when and where they occur, as well as estimates of any fatalities, injuries, and property damage.

We first download the data from web resource and unzip it. The data is comma-separated-value (CSV) file compressed via the bzip2 (size: 47 Mb).

```r
zip.file.url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"
data.dir <- "./data"
     
if (!file.exists(data.dir)) {
     dir.create(data.dir)
}

zip.file <- paste0(data.dir, "/", "StormData.csv.bz2")
if (!file.exists(zip.file)) {
     download.file(zip.file.url, zip.file)
}

csv.file <- paste0(data.dir, "/", "StormData.csv")
if (!file.exists(csv.file)) {
     unzip(zip.file, exdir = data.dir)
}
```

Then read the csv file:

```r
library(readr)
data <- read_csv(csv.file)
```

```
## Parsed with column specification:
## cols(
##   .default = col_character(),
##   STATE__ = col_double(),
##   COUNTY = col_double(),
##   BGN_RANGE = col_double(),
##   COUNTY_END = col_double(),
##   END_RANGE = col_double(),
##   LENGTH = col_double(),
##   WIDTH = col_double(),
##   F = col_integer(),
##   MAG = col_double(),
##   FATALITIES = col_double(),
##   INJURIES = col_double(),
##   PROPDMG = col_double(),
##   CROPDMG = col_double(),
##   LATITUDE = col_double(),
##   LONGITUDE = col_double(),
##   LATITUDE_E = col_double(),
##   LONGITUDE_ = col_double(),
##   REFNUM = col_double()
## )
```

```
## See spec(...) for full column specifications.
```

After reading we look at the structure of our data:

```r
library(dplyr)
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
glimpse(data)
```

```
## Observations: 902,297
## Variables: 37
## $ STATE__    <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, ...
## $ BGN_DATE   <chr> "4/18/1950 0:00:00", "4/18/1950 0:00:00", "2/20/195...
## $ BGN_TIME   <chr> "0130", "0145", "1600", "0900", "1500", "2000", "01...
## $ TIME_ZONE  <chr> "CST", "CST", "CST", "CST", "CST", "CST", "CST", "C...
## $ COUNTY     <dbl> 97, 3, 57, 89, 43, 77, 9, 123, 125, 57, 43, 9, 73, ...
## $ COUNTYNAME <chr> "MOBILE", "BALDWIN", "FAYETTE", "MADISON", "CULLMAN...
## $ STATE      <chr> "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL", "AL...
## $ EVTYPE     <chr> "TORNADO", "TORNADO", "TORNADO", "TORNADO", "TORNAD...
## $ BGN_RANGE  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ BGN_AZI    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ BGN_LOCATI <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ END_DATE   <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ END_TIME   <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ COUNTY_END <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ COUNTYENDN <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ END_RANGE  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ END_AZI    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ END_LOCATI <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ LENGTH     <dbl> 14.0, 2.0, 0.1, 0.0, 0.0, 1.5, 1.5, 0.0, 3.3, 2.3, ...
## $ WIDTH      <dbl> 100, 150, 123, 100, 150, 177, 33, 33, 100, 100, 400...
## $ F          <int> 3, 2, 2, 2, 2, 2, 2, 1, 3, 3, 1, 1, 3, 3, 3, 4, 1, ...
## $ MAG        <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ FATALITIES <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 4, 0, ...
## $ INJURIES   <dbl> 15, 0, 2, 2, 2, 6, 1, 0, 14, 0, 3, 3, 26, 12, 6, 50...
## $ PROPDMG    <dbl> 25.0, 2.5, 25.0, 2.5, 2.5, 2.5, 2.5, 2.5, 25.0, 25....
## $ PROPDMGEXP <chr> "K", "K", "K", "K", "K", "K", "K", "K", "K", "K", "...
## $ CROPDMG    <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ...
## $ CROPDMGEXP <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ WFO        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ STATEOFFIC <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ ZONENAMES  <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ LATITUDE   <dbl> 3040, 3042, 3340, 3458, 3412, 3450, 3405, 3255, 333...
## $ LONGITUDE  <dbl> 8812, 8755, 8742, 8626, 8642, 8748, 8631, 8558, 874...
## $ LATITUDE_E <dbl> 3051, 0, 0, 0, 0, 0, 0, 0, 3336, 3337, 3402, 3404, ...
## $ LONGITUDE_ <dbl> 8806, 0, 0, 0, 0, 0, 0, 0, 8738, 8737, 8644, 8640, ...
## $ REMARKS    <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,...
## $ REFNUM     <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, ...
```

Since we want to explore economical and public health consequences of weather events, subset our dataset using [National Weather Service Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf). We are basically interested in these variables listed below:

- *Fatalities/Injuries*. The determination of direct versus indirect causes of weather-related
fatalities or injuries is one of the most difficult aspects of Storm Data preparation.

- *Damage*. Property damage estimates should be entered as actual dollar amounts, if a
reasonably accurate estimate from an insurance company or other qualified individual is
available. 

- *Crop Damage Data*. Crop damage information may be obtained from reliable sources,
such as the U.S. Department of Agriculture (USDA), the county/parish agricultural extension
agent, the state department of agriculture, crop insurance agencies, or any other reliable
authority. 


```r
data <- select(data, BGN_DATE, STATE, EVTYPE, FATALITIES, INJURIES, PROPDMG, CROPDMG)
tail(data)
```

```
## # A tibble: 6 x 7
##             BGN_DATE STATE         EVTYPE FATALITIES INJURIES PROPDMG
##                <chr> <chr>          <chr>      <dbl>    <dbl>   <dbl>
## 1 11/28/2011 0:00:00    TN WINTER WEATHER          0        0       0
## 2 11/30/2011 0:00:00    WY      HIGH WIND          0        0       0
## 3 11/10/2011 0:00:00    MT      HIGH WIND          0        0       0
## 4  11/8/2011 0:00:00    AK      HIGH WIND          0        0       0
## 5  11/9/2011 0:00:00    AK       BLIZZARD          0        0       0
## 6 11/28/2011 0:00:00    AL     HEAVY SNOW          0        0       0
## # ... with 1 more variables: CROPDMG <dbl>
```

Do some tidying on data:

```r
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
library(tidyr)

data <- separate(data, BGN_DATE, into = c("Date", "Time"), sep = "\\W+") %>% 
          mutate(Date = mdy(Date)) %>% 
            select(-Time)
```

```
## Warning: Too many values at 902297 locations: 1, 2, 3, 4, 5, 6, 7, 8, 9,
## 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, ...
```

```
## Warning: All formats failed to parse. No formats found.
```

```r
data$STATE <- factor(data$STATE)
data$EVTYPE <- factor(data$EVTYPE)
data
```

```
## # A tibble: 902,297 x 7
##      Date  STATE  EVTYPE FATALITIES INJURIES PROPDMG CROPDMG
##    <date> <fctr>  <fctr>      <dbl>    <dbl>   <dbl>   <dbl>
##  1     NA     AL TORNADO          0       15    25.0       0
##  2     NA     AL TORNADO          0        0     2.5       0
##  3     NA     AL TORNADO          0        2    25.0       0
##  4     NA     AL TORNADO          0        2     2.5       0
##  5     NA     AL TORNADO          0        2     2.5       0
##  6     NA     AL TORNADO          0        6     2.5       0
##  7     NA     AL TORNADO          0        1     2.5       0
##  8     NA     AL TORNADO          0        0     2.5       0
##  9     NA     AL TORNADO          1       14    25.0       0
## 10     NA     AL TORNADO          0        0    25.0       0
## # ... with 902,287 more rows
```

```r
names(data) <- c("Event.Date", "State", "Event.Type", "Fatalities", "Injuries", "Property.Damage", "Crop.Damage")

summary(data)
```

```
##    Event.Date         State                    Event.Type    
##  Min.   :NA       TX     : 83728   HAIL             :288661  
##  1st Qu.:NA       KS     : 53440   TSTM WIND        :219944  
##  Median :NA       OK     : 46802   THUNDERSTORM WIND: 82563  
##  Mean   :NA       MO     : 35648   TORNADO          : 60652  
##  3rd Qu.:NA       IA     : 31069   FLASH FLOOD      : 54278  
##  Max.   :NA       NE     : 30271   FLOOD            : 25326  
##  NA's   :902297   (Other):621339   (Other)          :170873  
##    Fatalities          Injuries         Property.Damage  
##  Min.   :  0.0000   Min.   :   0.0000   Min.   :   0.00  
##  1st Qu.:  0.0000   1st Qu.:   0.0000   1st Qu.:   0.00  
##  Median :  0.0000   Median :   0.0000   Median :   0.00  
##  Mean   :  0.0168   Mean   :   0.1557   Mean   :  12.06  
##  3rd Qu.:  0.0000   3rd Qu.:   0.0000   3rd Qu.:   0.50  
##  Max.   :583.0000   Max.   :1700.0000   Max.   :5000.00  
##                                                          
##   Crop.Damage     
##  Min.   :  0.000  
##  1st Qu.:  0.000  
##  Median :  0.000  
##  Mean   :  1.527  
##  3rd Qu.:  0.000  
##  Max.   :990.000  
## 
```

###Results
__________
