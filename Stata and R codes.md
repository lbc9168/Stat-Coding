# Code notes 

## Stata

### bysort
   `bysort` command sort the dataset by its following variables, then execute following commands by sorted groups.

   For example:
   ```Stata
   bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)
   bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
   ```

### Generate quartly date according to monthly date
   ```Stata
   gen qdate = qofd(dofm(ym(year,month)))
   format %tq qdate
   ```
   
   `qdate` will be exported as character when dataset being exported as .csv to R


## R

### Aggregate observations

   Suppose we have a dataset like this:
   
   ```
     Year Month STATECD COUNTYCD       ppt     tmax      tmin
   1 1981    01      01      001  41.14090 11.95955 -2.070000
   2 1981    02      01      001 185.39970 15.51925  2.804179
   3 1981    03      01      001 152.04911 19.50493  4.755821
   4 1981    04      01      001 130.14433 26.73687 12.885373
   5 1981    05      01      001  79.19776 27.13030 13.716716
   6 1981    06      01      001  48.87418 33.49761 21.586269
   ```
   
   We want to calculate the average of ppt, tmax, and tmin for the same year in the same county.
   
   We need command `ddply` in package `plyr`:
   
   ```R
   library(plyr)
   Prism_test <- ddply(Prism_monthly_climate,.(Year,STATECD,COUNTYCD), summarize, mean_ppt = round(mean(ppt),2),
                       mean_tmax = round(mean(tmax),2), mean_tmin = round(mean(tmin),2))
   ```
   or simply use command `aggregate`:
   
   ```R
   temp <- aggregate(cbind(ppt,tmax,tmin)~STATECD+COUNTYCD+Year, PRISM_monthly_climate, mean)
   ```
   
   then we get:
   ```
     Year STATECD COUNTYCD mean_ppt mean_tmax mean_tmin
   1 1981      01      001   100.09     23.99     10.93
   2 1981      01      003   102.71     25.27     12.53
   3 1981      01      005    96.77     24.66     10.71
   4 1981      01      007    91.57     23.79      9.59
   5 1981      01      009    98.45     22.34      8.57
   6 1981      01      011    95.18     24.21     10.35
   ```

### Create quarter variable
   `quarter` command is only useful when date variable is in a "date" format. It doesn't work with numeric month or year format.
   
   We can create a quarter map dataset and merge it with original data
   
   ```R
   quarters.map<-data.frame(month=1:12,quarter=rep(1:4,each=3))
   Climate_data <- merge(Climate_data, quarters.map)
   ```

### Merge data
   Data to be merged are better to be the same form (data.frame).
   
   When name of key variables in both datasets are the same:
   ```R
   Area_loblolly_reg_wclimate <- merge(Area_loblolly_reg, PRISM_all_states_wide, by = c("year", "statecd", "countycd"))
   ```
   
   When name of key variables in both datasets are NOT the same:
   ```R
   Area_loblolly_reg_wclimate <- merge(x = Area_loblolly_reg, y = PRISM_all_states_wide, 
                                       by.x = c("year", "statecd", "countycd"), by.y = c("Year", "Stateid", "Countyid"))
   ```

   
### Remove rows or columns in dataset
   We can use `select` to remove columns, use `subset` to remove rows.
   
   ```R
   # remove column
   PRISM_all_states_wide <- select(PRISM_all_states_wide, -date)
   quarterly_climate_clean <- subset(quarterly_climate_clean, select = -c(qdate, X1))
   # remove rows based on value
   PRISM_all_states_wide <- subset(PRISM_all_states_wide, year > 1960)
   ```


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

   We can use command  `reshape` to convert multiple value variables, for example:
   ```
     STATECD COUNTYCD year quarter       ppt     tmean
   1       1        1 1961       1 239.55892 10.623028
   2       4        1 1961       1  30.47994  0.448080
   3       5        1 1961       1 167.82879  8.861856
   4       6        1 1961       1  49.81333  8.033334
   5       9        1 1961       1  86.21370 -1.671182
   6      10        1 1961       1 104.60288  2.616061
   ```
   We want to convert both `ppt` and `tmean` based on `quarter`, we can use:
   ```R
   Climate_quartly <- reshape(temp, direction = "wide", idvar = c("STATECD", "COUNTYCD", "year"), timevar = "quarter")
   ```
   then we get:
   ```
     STATECD COUNTYCD year     ppt.1   tmean.1      ppt.2  tmean.2     ppt.3  tmean.3     ppt.4   tmean.4
   1       1        1 1961 239.55892 10.623028 112.411127 20.18103 96.060141 25.53272 177.27089 13.682981
   2       4        1 1961  30.47994  0.448080   8.122142 12.36430 60.115458 17.16503  54.98032  2.445351
   3       5        1 1961 167.82879  8.861856 105.181363 20.01030 80.963712 25.03409 164.55530 11.342235
   4       6        1 1961  49.81333  8.033334  24.060001 14.63000  3.053333 21.77500  38.53000 11.550000
   5       9        1 1961  86.21370 -1.671182 128.884970 13.14818 96.742606 21.32861  73.28473  6.165485
   6      10        1 1961 104.60288  2.616061 107.787424 15.90629 73.373788 23.78273  81.94045  8.524091
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

