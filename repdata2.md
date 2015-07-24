# Economic and Public Health Impact of Storm Events


# Economic and Public Health Impact of Storm Events
Consider writing your report as if it were to be read by a government or municipal manager who might be responsible for preparing for severe weather events and will need to prioritize resources for different types of events.

Across the United States, which types of events (as indicated in the EVTYPE variable) are most harmful with respect to population health?

Across the United States, which types of events have the greatest economic consequences?

## Install and Load Libraries

```r
# Auto Install Packages
list.of.packages <- c("dplyr", "ggplot2", "knitr", "lubridate")
new.packages <- list.of.packages[!(list.of.packages %in% installed.packages()[,"Package"])]

if(length(new.packages)) install.packages(new.packages)

## Bring in the libs.
library(dplyr)
library(ggplot2)
library(knitr)

## Knitr setup for rounding:
options(scipen = 1, digits = 1)
```


## Synopsis
In this report, I aim to describe the health impact and economic impacts of various event types in the places where they occur.  

## Data Processing 

We first read in the storm data from the raw CSV file included in the zip archive. 


```r
## Results
stormData <- read.csv('data//repdata-data-StormData.csv.bz2')
```

After reading in the data, we check the first few rows.  There are 902297 in the data set, so we take a look at the first few:


```r
head(stormData)
```

```
##   STATE__           BGN_DATE BGN_TIME TIME_ZONE COUNTY COUNTYNAME STATE
## 1       1  4/18/1950 0:00:00     0130       CST     97     MOBILE    AL
## 2       1  4/18/1950 0:00:00     0145       CST      3    BALDWIN    AL
## 3       1  2/20/1951 0:00:00     1600       CST     57    FAYETTE    AL
## 4       1   6/8/1951 0:00:00     0900       CST     89    MADISON    AL
## 5       1 11/15/1951 0:00:00     1500       CST     43    CULLMAN    AL
## 6       1 11/15/1951 0:00:00     2000       CST     77 LAUDERDALE    AL
##    EVTYPE BGN_RANGE BGN_AZI BGN_LOCATI END_DATE END_TIME COUNTY_END
## 1 TORNADO         0                                               0
## 2 TORNADO         0                                               0
## 3 TORNADO         0                                               0
## 4 TORNADO         0                                               0
## 5 TORNADO         0                                               0
## 6 TORNADO         0                                               0
##   COUNTYENDN END_RANGE END_AZI END_LOCATI LENGTH WIDTH F MAG FATALITIES
## 1         NA         0                      14.0   100 3   0          0
## 2         NA         0                       2.0   150 2   0          0
## 3         NA         0                       0.1   123 2   0          0
## 4         NA         0                       0.0   100 2   0          0
## 5         NA         0                       0.0   150 2   0          0
## 6         NA         0                       1.5   177 2   0          0
##   INJURIES PROPDMG PROPDMGEXP CROPDMG CROPDMGEXP WFO STATEOFFIC ZONENAMES
## 1       15      25          K       0                                    
## 2        0       2          K       0                                    
## 3        2      25          K       0                                    
## 4        2       2          K       0                                    
## 5        2       2          K       0                                    
## 6        6       2          K       0                                    
##   LATITUDE LONGITUDE LATITUDE_E LONGITUDE_ REMARKS REFNUM
## 1     3040      8812       3051       8806              1
## 2     3042      8755          0          0              2
## 3     3340      8742          0          0              3
## 4     3458      8626          0          0              4
## 5     3412      8642          0          0              5
## 6     3450      8748          0          0              6
```

Now let's look at some of the column headings:

```r
names(stormData)
```

```
##  [1] "STATE__"    "BGN_DATE"   "BGN_TIME"   "TIME_ZONE"  "COUNTY"    
##  [6] "COUNTYNAME" "STATE"      "EVTYPE"     "BGN_RANGE"  "BGN_AZI"   
## [11] "BGN_LOCATI" "END_DATE"   "END_TIME"   "COUNTY_END" "COUNTYENDN"
## [16] "END_RANGE"  "END_AZI"    "END_LOCATI" "LENGTH"     "WIDTH"     
## [21] "F"          "MAG"        "FATALITIES" "INJURIES"   "PROPDMG"   
## [26] "PROPDMGEXP" "CROPDMG"    "CROPDMGEXP" "WFO"        "STATEOFFIC"
## [31] "ZONENAMES"  "LATITUDE"   "LONGITUDE"  "LATITUDE_E" "LONGITUDE_"
## [36] "REMARKS"    "REFNUM"
```

In order to understand the health and economic impacts of the events, we'll need event information, location information and health and economic impact information.  Let's select only those columns from the dataset:

In addition, NOA data goes back to 1950, but according to the assignment, most recent years should be considered more complete.  On the other hand, we want to be able to see long term effects of events as well.  Therefore, we will subset this data only to include events after 1980.  

Lastly, we will do some processing of the data to make sure it's in the correct type for R.


```r
stormDataClean <- stormData %>%
    select(
        BGN_DATE, # Date the event started
        COUNTYNAME, # Name of the County
        STATE, # Abbreviation of the state
        EVTYPE, # Event type
        END_DATE, # END date
        FATALITIES, # Fatalities
        INJURIES, # Injuries
        PROPDMG, # Property Damage
        PROPDMGEXP, # Magnitude Number (K = thousands, M = Millions, etc)
        CROPDMG, # Crop Damage
        CROPDMGEXP # Magnitude Number (K = thousands, M = Millions, etc)
    )
```


