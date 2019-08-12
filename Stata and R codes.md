# Code notes 

## Stata

### bysort
`bysort` command sort the dataset by its following variables, then execute following commands by sorted groups.

For example:
```Stata
bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)
bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
```

## R

### reshape2 package
(Reference: https://www.r-bloggers.com/reshape-and-aggregate-data-with-the-r-package-reshape2/)

`reshape` package works on reshapping data between wide-format and long-format

For example:

Wide-format:
```
   fips   date       ppt     tmax      tmin
1 01001 198101  41.14090 11.95955 -2.070000
2 01001 198102 185.39970 15.51925  2.804179
```

Long-format:
```
  fips  date   var     value
1 01001 198101 ppt     41.1 
2 01001 198101 tmax    12.0 
3 01001 198101 tmin    -2.07
4 01001 198102 ppt    185.  
5 01001 198102 tmax    15.5 
6 01001 198102 tmin     2.80
```

We use `dcast` command to convert long-format to wide-format:
```R
wide <- dcast(long, fips + date ~ var)
```
It means to keep fips and date as identification variables, and use values in "var" as new variable names in wide-format.

If we want to convert wide-format to long-format, we use `melt`
```R
long <- melt(wide, id = c("fips", "date"))
```
