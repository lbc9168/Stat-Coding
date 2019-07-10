# Using loop to write queries and retrieve results


These are required libraries

```R
library(RSQLite)
library(plyr)
library(reshape2)
library(ggplot2)
library(DBI)
```


The purpose of following script is to solve the problem that `dbGetQuery` cannot identify `s.INVYR = i` directly in `SELECT * FROM ***` script 

```R

i <- 2014

repeat{
  selected_year <- i
  selected_next_year <- i+1

  sql <- sqlInterpolate(FIADB, "SELECT s.STATECD, s.PLOT, s.AGEDIA, s.HT, s.INVYR, 
                                          p.LAT, p.LON
                                          
                                          From SITETREE s, PLOT p
                                          
                                          WHERE p.CN = s.PLT_CN
                                          AND s.SPGRPCD = 2
                                          
                                          AND (s.STATECD = 37 OR s.STATECD = 45 OR s.STATECD = 12 
                                          OR s.STATECD = 47 OR s.STATECD = 13 OR s.STATECD = 1 OR s.STATECD = 28)
                                                                                
                                          AND (s.INVYR = ?year OR s.INVYR = ?nextyear)"
                                          # the question mark is to transfer a number to a column factor
                                                                                  
                                          , year = selected_year, nextyear = selected_next_year
                                          
                                           ) 
  sql
  
  assign(paste("loblolly", i, sep = "_") , dbGetQuery(FIADB, sql))
  
  i <- i-2
  
  if(i<2002){break}
}

```
