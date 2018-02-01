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
available. Estimates should be rounded to
three significant digits, followed by an alphabetical character signifying the magnitude of the
number, i.e., 1.55B for $1,550,000,000. Alphabetical characters used to signify magnitude
include “K” for thousands, “M” for millions, and “B” for billions.

- *Crop Damage Data*. Crop damage information may be obtained from reliable sources,
such as the U.S. Department of Agriculture (USDA), the county/parish agricultural extension
agent, the state department of agriculture, crop insurance agencies, or any other reliable
authority. 

Look at possible damage multiplier values:

```r
table(data$PROPDMGEXP)
```

```
## 
##      -      ?      +      0      1      2      3      4      5      6 
##      1      8      5    216     25     13      4      4     28      4 
##      7      8      B      h      H      K      m      M 
##      5      1     40      1      6 424665      7  11330
```

```r
table(data$CROPDMGEXP)
```

```
## 
##      ?      0      2      B      k      K      m      M 
##      7     19      1      9     21 281832      1   1994
```

Look at possible weather events:

```r
Event.types <- sort(unique(data$EVTYPE))
length(Event.types)
```

```
## [1] 977
```

```r
Event.types[c(1, 45:48, 237:239, 351:354)]
```

```
##  [1] "?"                "Coastal Flood"    "COASTAL FLOOD"   
##  [4] "coastal flooding" "Coastal Flooding" "HAIL 0.75"       
##  [7] "HAIL 0.88"        "HAIL 075"         "HIGH WIND"       
## [10] "HIGH WIND (G40)"  "HIGH WIND 48"     "HIGH WIND 63"
```
There is a lot of messy values in the `EVTYPE` variable. We should try to clean it.

First We select only necessary variables:

```r
data <- select(data, BGN_DATE, EVTYPE, FATALITIES, INJURIES, PROPDMG, CROPDMG, PROPDMGEXP, CROPDMGEXP)
tail(data)
```

```
## # A tibble: 6 x 8
##             BGN_DATE         EVTYPE FATALITIES INJURIES PROPDMG CROPDMG
##                <chr>          <chr>      <dbl>    <dbl>   <dbl>   <dbl>
## 1 11/28/2011 0:00:00 WINTER WEATHER          0        0       0       0
## 2 11/30/2011 0:00:00      HIGH WIND          0        0       0       0
## 3 11/10/2011 0:00:00      HIGH WIND          0        0       0       0
## 4  11/8/2011 0:00:00      HIGH WIND          0        0       0       0
## 5  11/9/2011 0:00:00       BLIZZARD          0        0       0       0
## 6 11/28/2011 0:00:00     HEAVY SNOW          0        0       0       0
## # ... with 2 more variables: PROPDMGEXP <chr>, CROPDMGEXP <chr>
```

Convert the `PROPDMGEXP, CROPDMGEXP, EVTYPE` values to upper case, exclude some meaningless event types:

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

data <- separate(data, BGN_DATE, into = c("Date", "Time"), sep = "\\s+") %>% 
          mutate(Date = mdy(Date), 
                 PROPDMGEXP = toupper(PROPDMGEXP), 
                 CROPDMGEXP = toupper(CROPDMGEXP),
                 EVTYPE = toupper(EVTYPE)) %>% 
            select(-Time) %>% 
              filter(!grepl("^SUMMARY|^\\?|NONE" , EVTYPE))

head(data)
```

```
## # A tibble: 6 x 8
##         Date  EVTYPE FATALITIES INJURIES PROPDMG CROPDMG PROPDMGEXP
##       <date>   <chr>      <dbl>    <dbl>   <dbl>   <dbl>      <chr>
## 1 1950-04-18 TORNADO          0       15    25.0       0          K
## 2 1950-04-18 TORNADO          0        0     2.5       0          K
## 3 1951-02-20 TORNADO          0        2    25.0       0          K
## 4 1951-06-08 TORNADO          0        2     2.5       0          K
## 5 1951-11-15 TORNADO          0        2     2.5       0          K
## 6 1951-11-15 TORNADO          0        6     2.5       0          K
## # ... with 1 more variables: CROPDMGEXP <chr>
```

Clean the `EVTYPE` values:

```r
data$EVTYPE <- gsub("&", "AND", data$EVTYPE) 
data$EVTYPE <- gsub("\\s{2,}", " ", data$EVTYPE) 
data$EVTYPE <- gsub("\\\\", "/", data$EVTYPE) 
data$EVTYPE <- gsub("\\s+AND\\s+", "/", data$EVTYPE) 
data$EVTYPE <- gsub("^VOLCANIC ASH.*", "VOLCANIC ASH", data$EVTYPE) 
data$EVTYPE <- gsub("^TSTM W[I]*ND.*|^TSTM.?$|^T[H]?U[N]?[DE].*[S]?[T]?[OR]+M.*W[IN]*.*", "THUNDERSTORM WINDS", data$EVTYPE) 
data$EVTYPE <- gsub("^TORN[AD]?[DA]?O.*|.+TORNADO.*", "TORNADO", data$EVTYPE) 
data$EVTYPE <- gsub("^HURRICANE.*", "HURRICANE", data$EVTYPE) 
data$EVTYPE <- gsub("^HIGH WIND[S]?\\s*\\d+", "HIGH WIND", data$EVTYPE) 
data$EVTYPE <- gsub("WA[Y]?TER\\s?SPOUT[S]?", "WATERSPOUTS", data$EVTYPE) 
data$EVTYPE <- gsub("(SML)\\s?", "SMALL", data$EVTYPE)
data$EVTYPE <- gsub("UNSEASONABLY", "UNSEASONABLE", data$EVTYPE)
data$EVTYPE <- gsub("COOL", "COLD", data$EVTYPE)
data$EVTYPE <- gsub("THUNDERSTORMS", "THUNDERSTORM", data$EVTYPE)
data$EVTYPE <- gsub("W[I]?ND[S]?", "WIND", data$EVTYPE) 
data$EVTYPE <- gsub("HAIL.{1}\\d+\\W?\\d*", "HAIL", data$EVTYPE) 
data$EVTYPE <- gsub("\\W+$|-\\s+", "", data$EVTYPE) 

length(unique(data$EVTYPE))
```

```
## [1] 609
```

Apply damage multipliers:

```r
data <- mutate(data, 
               PROPDMG = PROPDMG * if_else(PROPDMGEXP == "K", 10 ^ 3,
                                     if_else(PROPDMGEXP == "H", 10 ^ 2,
                                       if_else(PROPDMGEXP == "M", 10 ^ 6, 
                                         if_else(PROPDMGEXP == "B", 10 ^ 9, 1)
                                         , 1), 1), 1)) %>% 
          mutate(CROPDMG = CROPDMG * if_else(CROPDMGEXP == "K", 10 ^ 3,
                                       if_else(CROPDMGEXP == "H", 10 ^ 2,
                                         if_else(CROPDMGEXP == "M", 10 ^ 6, 
                                           if_else(CROPDMGEXP == "B", 10 ^ 9, 1)
                                           , 1), 1), 1)) %>% 
            select(-ends_with("EXP"))

data     
```

```
## # A tibble: 902,219 x 6
##          Date  EVTYPE FATALITIES INJURIES PROPDMG CROPDMG
##        <date>   <chr>      <dbl>    <dbl>   <dbl>   <dbl>
##  1 1950-04-18 TORNADO          0       15   25000       0
##  2 1950-04-18 TORNADO          0        0    2500       0
##  3 1951-02-20 TORNADO          0        2   25000       0
##  4 1951-06-08 TORNADO          0        2    2500       0
##  5 1951-11-15 TORNADO          0        2    2500       0
##  6 1951-11-15 TORNADO          0        6    2500       0
##  7 1951-11-16 TORNADO          0        1    2500       0
##  8 1952-01-22 TORNADO          0        0    2500       0
##  9 1952-02-13 TORNADO          1       14   25000       0
## 10 1952-02-13 TORNADO          0        0   25000       0
## # ... with 902,209 more rows
```

Set meaningful names for the variables:

```r
names(data) <- c("Date", "Event.Type", "Fatalities", "Injuries", "Property.Damage", "Crop.Damage")
```

Check for NA values:

```r
sum(is.na(data))
```

```
## [1] 0
```

Create new dataset for public health consequences:

```r
Events.Cause.Fatalities <- select(data, Event.Type, Fatalities) %>% 
                             group_by(Event.Type) %>% 
                               summarise(Total.Fatalities = sum(Fatalities)) %>% 
                                 arrange(desc(Total.Fatalities))
Events.Cause.Fatalities
```

```
## # A tibble: 609 x 2
##           Event.Type Total.Fatalities
##                <chr>            <dbl>
##  1           TORNADO             5661
##  2    EXCESSIVE HEAT             1903
##  3       FLASH FLOOD              978
##  4              HEAT              937
##  5         LIGHTNING              817
##  6 THUNDERSTORM WIND              710
##  7             FLOOD              470
##  8       RIP CURRENT              368
##  9         HIGH WIND              283
## 10         AVALANCHE              224
## # ... with 599 more rows
```

```r
Events.Cause.Injuries <- select(data, Event.Type, Injuries) %>% 
                                group_by(Event.Type) %>% 
                                  summarise(Total.Injuries = sum(Injuries)) %>% 
                                    arrange(desc(Total.Injuries))
Events.Cause.Injuries
```

```
## # A tibble: 609 x 2
##           Event.Type Total.Injuries
##                <chr>          <dbl>
##  1           TORNADO          91407
##  2 THUNDERSTORM WIND           9496
##  3             FLOOD           6789
##  4    EXCESSIVE HEAT           6525
##  5         LIGHTNING           5230
##  6              HEAT           2100
##  7         ICE STORM           1975
##  8       FLASH FLOOD           1777
##  9         HIGH WIND           1440
## 10              HAIL           1361
## # ... with 599 more rows
```

Create new dataset for economic consequences:

```r
Events.Cause.Property.Damage <- select(data, Event.Type, Property.Damage) %>% 
                                  group_by(Event.Type) %>% 
                                    summarise(Total.Property.Damage = sum(Property.Damage)) %>% 
                                      arrange(desc(Total.Property.Damage))
Events.Cause.Property.Damage
```

```
## # A tibble: 609 x 2
##           Event.Type Total.Property.Damage
##                <chr>                 <dbl>
##  1             FLOOD          144657709807
##  2         HURRICANE           84756180010
##  3           TORNADO           58593098529
##  4       STORM SURGE           43323536000
##  5       FLASH FLOOD           16141362067
##  6              HAIL           15732819543
##  7 THUNDERSTORM WIND            9761990706
##  8    TROPICAL STORM            7703890550
##  9      WINTER STORM            6688497251
## 10         HIGH WIND            5878433043
## # ... with 599 more rows
```

```r
Events.Cause.Crop.Damage <- select(data, Event.Type, Crop.Damage) %>% 
                              group_by(Event.Type) %>% 
                                summarise(Total.Crop.Damage = sum(Crop.Damage)) %>% 
                                  arrange(desc(Total.Crop.Damage))
Events.Cause.Crop.Damage
```

```
## # A tibble: 609 x 2
##           Event.Type Total.Crop.Damage
##                <chr>             <dbl>
##  1           DROUGHT       13972566000
##  2             FLOOD        5661968450
##  3         HURRICANE        5515292800
##  4       RIVER FLOOD        5029459000
##  5         ICE STORM        5022113500
##  6              HAIL        3026044473
##  7       FLASH FLOOD        1421317100
##  8      EXTREME COLD        1312973000
##  9 THUNDERSTORM WIND        1224408988
## 10      FROST/FREEZE        1094186000
## # ... with 599 more rows
```

###Results
__________
