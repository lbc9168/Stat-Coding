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
*(Reference: https://www.r-bloggers.com/reshape-and-aggregate-data-with-the-r-package-reshape2/)*

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

### Split a variable
Suppose we have a dataset (d_clean) like this:
```
   fips   date       ppt     tmax      tmin
1 01001 198101  41.14090 11.95955 -2.070000
2 01001 198102 185.39970 15.51925  2.804179
3 01001 198103 152.04911 19.50493  4.755821
4 01001 198104 130.14433 26.73687 12.885373
5 01001 198105  79.19776 27.13030 13.716716
6 01001 198106  48.87418 33.49761 21.586269
```
Now we want to split the fips variable into state code and county code. The first two digits are state codes, the last three digits are county codes.

We can run following codes:
```R
Prism_monthly_climate_clean <- cbind(read.fwf(file = textConnection(as.character(d_clean[, 1])), 
                                 widths = c(2, 3), colClasses = "character", 
                                 col.names = c("STATECD", "COUNTYCD")), d_clean[-1]
                                )
```
Then we get:
```
  STATECD COUNTYCD   date       ppt     tmax      tmin
1      01      001 198101  41.14090 11.95955 -2.070000
2      01      001 198102 185.39970 15.51925  2.804179
3      01      001 198103 152.04911 19.50493  4.755821
4      01      001 198104 130.14433 26.73687 12.885373
5      01      001 198105  79.19776 27.13030 13.716716
6      01      001 198106  48.87418 33.49761 21.586269
```
