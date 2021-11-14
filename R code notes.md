# R Code notes 


## Add a Column to Dataframe Based on Other Columns with dplyr
*(Reference: https://www.marsja.se/r-add-column-to-dataframe-based-on-other-columns-conditions-dplyr/)*

Should have the `dplyr` package installed.

## Add rows with missing years by group
*(Reference: https://stackoverflow.com/questions/43799109/add-rows-with-missing-years-by-group)*

We have a data frame like this:
```R
firm type year value
A    X    2000     1
A    X    2004     3
A    X    2007     7
```

If we want to fill in the missing years by firm and type, and fill in the missing values, we can get the final table like this:

```R
firm type year value
A    X    2000     1
A    X    2001     1
A    X    2002     1
A    X    2003     1
A    X    2004     3
A    X    2005     3
A    X    2006     3
A    X    2007     7
A    X    2008     7
A    X    2009     7
A    X    2010     7
```

We can use code like this:
```R
library(dplyr)
library(tidyr)

minimal %>% 
  group_by(firm, type) %>% 
  complete(year = full_seq(2000:2010, 1)) %>% 
  fill(value)
```

### Create a new variable by adding two columns
```R
south_planting <- south_planting %>%
  mutate(plantYear = sampleYear - STDAGE)
```

### Add a Column to a Dataframe Based on Other Column
```R
# Adding column based on other column:
depr_df %>%
    mutate(Status = case_when(
    endsWith(ID, "R") ~ "Recovered",
    endsWith(ID, "S") ~ "Sick"
    ))
```

## Aggregate observations

   *(Reference: https://stackoverflow.com/questions/21982987/mean-per-group-in-a-data-frame)*
   
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
   
   We want to calculate the average of ppt, tmax, and tmin grouped by year and county.
   
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
   
## Average per group

   Needs package `plyr`.
   
   Suppose we have a dataset `climate_eastern`:
   
   ```
     year STATECD COUNTYCD       ppt     tmean season
   1 1895       1        1 185.83761  7.673521      w
   2 1895       1        1  71.03254  4.110563      w
   3 1895       1        1 206.70070 13.856620     sp
   4 1895       1        1  98.70648 18.183873     sp
   5 1895       1        1  92.23239 21.652817     sp
   6 1895       1        1 101.24592 26.153098     su
   ```
   
   We want to calculate each season's average ppt and tmean.

   ```r
   climate_test <- ddply(climate_eastern, .(year, season, STATECD, COUNTYCD), summarize, ppt.avg=mean(ppt), tmean.avg=mean(tmean))
   ```
   
   Then we got:
   
   ```
     year season STATECD COUNTYCD  ppt.avg tmean.avg
   1 1895      f       1        1 40.39549  18.71430
   2 1895      f       1        3 62.19067  19.82170
   3 1895      f       1        5 33.28031  19.21043
   4 1895      f       1        7 56.11158  17.96872
   5 1895      f       1        9 64.95404  16.23894
   6 1895      f       1       11 33.52414  18.65489
   ```
   
## Calculate relative change
*(ref: https://stackoverflow.com/questions/56724064/calculate-relative-change-in-time-by-group)*

Relative change can be calculated using command `:=` library `data.table`

The dataframe should be converted to `data.table` type before appling command `:=`. We can run `help(":=")` for more information.

The data table is like:

```R
lpermno fyear sale
10065   2001    NA
10065   2002    NA
10065   2003    NA
10065   2004    NA
59328   2001    26539.000
59328   2002    26764.000
59328   2003    30141.000
59328   2004    34209.000
61241   2001    3891.754
61241   2002    2697.029
61241   2003    3519.168
61241   2004    5001.435
```

Use this command:

```R
dt[, pctchnge := sale / c(sale[1], head(sale, -1)), by="lpermno"][order(lpermno)]
```

`by=" "` means calculations are within groups in `lpermno`. 

And the results becomes:

```R
#     lpermno fyear      sale    output
#  1:   10065  2001        NA        NA
#  2:   10065  2002        NA        NA
#  3:   10065  2003        NA        NA
#  4:   10065  2004        NA        NA
#  5:   59328  2001 26539.000 1.0000000
#  6:   59328  2002 26764.000 1.0084781
#  7:   59328  2003 30141.000 1.1261770
#  8:   59328  2004 34209.000 1.1349657
#  9:   61241  2001  3891.754 1.0000000
# 10:   61241  2002  2697.029 0.6930112
# 11:   61241  2003  3519.168 1.3048314
# 12:   61241  2004  5001.435 1.4211981
```
   
## Character value with quote marks

To refer values with quote marks, use `\` in front of `"`.

For example, `pet.eval_typ = '\"EXPCURR\"'`

## Convert between numeric and character

Use `as.numeric` or `as.character`.

e.g. `south_planting[1] <- as.numeric(south_planting$sampleYear)`
   
## Count observations by group
*(ref: https://dplyr.tidyverse.org/reference/count.html)*

Need package `tidyverse`

```R
library(tidyverse)
df %>% count(dem01)
```
   
## Create new variable based on existing variables 

   ```R
   climate_eastern$season <- "w"
   climate_eastern$season[climate_eastern$month>=3 & climate_eastern$month<=5]<- "sp"
   climate_eastern$season[climate_eastern$month>=6 & climate_eastern$month<=8]<- "su" 
   climate_eastern$season[climate_eastern$month>=9 & climate_eastern$month<=11]<- "f"
   ```
   
   Commands in `[]` are conditions.

## Create quarter variable
   `quarter` command is only useful when date variable is in a "date" format. It doesn't work with numeric month or year format.
   
   We can create a quarter map dataset and merge it with original data
   
   ```R
   quarters.map<-data.frame(month=1:12,quarter=rep(1:4,each=3))
   Climate_data <- merge(Climate_data, quarters.map)
   ```

## Cut part of variable from the right
   There is few function in R that allows us to position from the right (maybe). So we can first create a function that make a subset from the right, then splitting. *(reference: https://rforexcelusers.com/how-to-mid-right-left-r/)*
   
   ```R
   left = function(text, num_char) {
          substr(text, 1, num_char)
          }
 
   mid = function(text, start_num, num_char) {
         substr(text, start_num, start_num + num_char - 1)
         }
 
   right = function(text, num_char) {
           substr(text, nchar(text) - (num_char-1), nchar(text))
           }
   ```
  
   Then do the cutting.
   
   ```R
   # replace the character
   south_planting[1] <- rightcut(south_planting$EVAL_GRP,4)

   # change variable name
   colnames(south_planting)[1] <- "plantYear"
   ```
   
   We can also try to split a variable by creating function.

## Factor function
  *(Reference: https://stats.idre.ucla.edu/r/modules/coding-for-categorical-variables-in-regression-models/)*
  
  When we want to create a factor variable based on an existing variable, we just need the command `factor`
  
  ```R
  # creating the factor variable
  hsb2$race.f <- factor(hsb2$race)
  ```


## Formula with numerous variables
  *(Reference: https://stackoverflow.com/questions/5774813/short-formula-call-for-many-variables-when-building-a-model)*
  
  If we want to create a long model with numerous variables, which is hard to input by hand. For now there is one way to make it:
  
  #### 1. Counstruct the formula manually with `paste` and `as.formula()`
  
  ```R
  xnam <- paste("x", 1:25, sep="")
  (fmla <- as.formula(paste("y ~ ", paste(xnam, collapse= "+"))))
  ```
  
  then insert this object into regression function: `lm(fmla, data = myData)`
  
  Or simply select variables by selecting columns from dataset:
  
  ```R
  xnam <- names(data.frame(myData[, 1:5], myData[, 9:13]))
  ```

  There is a weird thing when selecting the column. If we select one column like `names(myData[,2])`, the column name will lost, then we have to rename it.
    

## Identify and Remove Duplicate Data
   *(Reference: https://www.datanovia.com/en/lessons/identify-and-remove-duplicate-data-in-r/)*
   
   We need to load `library(tidyverse)` 
   
   Given the following vector:
   ```R
   x <- c(1, 1, 4, 5, 4, 6)
   ```
   `duplicated(x)` gives us the result:
   
   [1] FALSE  TRUE FALSE FALSE  TRUE FALSE
 
   `x[!duplicated(x)]` can remove all the duplicated elements in x
   
   The function `distinct() [dplyr package]` can be used to keep only unique/distinct rows from a data frame. If there are duplicate rows, only the first row is preserved. It’s an efficient version of the R base function `unique()`.

   ```R
   # Remove duplicate rows based on all columns:
   my_data %>% distinct()
   ```
   ```
   ## # A tibble: 149 x 5
   ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
   ##          <dbl>       <dbl>        <dbl>       <dbl> <fct>  
   ## 1          5.1         3.5          1.4         0.2 setosa 
   ## 2          4.9         3            1.4         0.2 setosa 
   ## 3          4.7         3.2          1.3         0.2 setosa 
   ## 4          4.6         3.1          1.5         0.2 setosa 
   ## 5          5           3.6          1.4         0.2 setosa 
   ## 6          5.4         3.9          1.7         0.4 setosa 
   ## # ... with 143 more rows
   ```
   Remove duplicate rows based on certain columns (variables):
   ```R
   # Remove duplicated rows based on Sepal.Length
   my_data %>% distinct(Sepal.Length, .keep_all = TRUE)
   ```
   ```
   ## # A tibble: 35 x 5
   ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
   ##          <dbl>       <dbl>        <dbl>       <dbl> <fct>  
   ## 1          5.1         3.5          1.4         0.2 setosa 
   ## 2          4.9         3            1.4         0.2 setosa 
   ## 3          4.7         3.2          1.3         0.2 setosa 
   ## 4          4.6         3.1          1.5         0.2 setosa 
   ## 5          5           3.6          1.4         0.2 setosa 
   ## 6          5.4         3.9          1.7         0.4 setosa 
   ## # ... with 29 more rows
   ```
   ```R
   # Remove duplicated rows based on 
   # Sepal.Length and Petal.Width
   my_data %>% distinct(Sepal.Length, Petal.Width, .keep_all = TRUE)
   ```
   ```
   ## # A tibble: 110 x 5
   ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
   ##          <dbl>       <dbl>        <dbl>       <dbl> <fct>  
   ## 1          5.1         3.5          1.4         0.2 setosa 
   ## 2          4.9         3            1.4         0.2 setosa 
   ## 3          4.7         3.2          1.3         0.2 setosa 
   ## 4          4.6         3.1          1.5         0.2 setosa 
   ## 5          5           3.6          1.4         0.2 setosa 
   ## 6          5.4         3.9          1.7         0.4 setosa 
   ## # ... with 104 more rows
   ```  

## Loop for Regression
   We can use `lapply` to loop a regression. 
   
   In this regression, we are changing the dependent variable from `list(i = as.name(x))`. 
   
   ```R
   softwood_sum <- lapply(east_softwood, function(x) {
                     glm(substitute(i ~ ppt.avg.f + ppt.avg.sp + ppt.avg.su + ppt.avg.w +                      
                     pop_num + PPI_hw + PPI_sw + private, list(i = as.name(x))), data = East_reg, family = binomial)
                   })

   lapply(softwood_sum, summary)
   ```
   
   In the example above, the results are stored in `softwood_sum`. `east_softwood` is a name list that stores names of dependent variables.

## Merge data
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
   
   (It looks like the sequence of x and y will not influence the the merge result. Should check that later.)
   
   ### Merge multiple dataset
   Use command `rbind` in `do.call`.
   
   ```R
   ufoMerged <- do.call("rbind", list(ufo1, ufo2, ufo3, ufo4))
   ```

## Moving average
   *(Reference: http://uc-r.github.io/ts_moving_averages
     and https://stackoverflow.com/questions/48068830/r-moving-average-per-group)*
   
   Moving averages is a smoothing approach that averages values from a window of consecutive time periods, thereby generating a series of averages.
   
   To compute moving avearge we can leverage the `rollmean` function from the `zoo` package. We will need the package `tidyverse` as well. Here is an example:
   
   We have a dataset like this:
   ```
       year COUNTYCD ppt.1 ppt.1.roll3
    1  1961        1 105.         NA  
    2  1962        1 116.         NA  
    3  1963        1  94.3       105. 
    4  1964        1  99.1       103. 
    5  1965        1  86.0        93.1
    6  1966        1 102.         95.9
    7  1967        1  58.7        82.4
    8  1968        1  70.3        77.2
   ```
   This is the finished version, we want to get the moving average number for variable `ppt.1`, grouped by `COUNTYCD`. And the variable `ppt.1.roll3` is the result. 
   ```R
   Climate_quartly_NC <- Climate_quartly_NC %>%
      group_by(COUNTYCD) %>%
      mutate(ppt.2.roll3 = rollmean(ppt.2, k = 3, fill = NA, align = "right"))
   ```
   
## Number (Identify) variables by group
*(Reference: https://stackoverflow.com/questions/12925063/numbering-rows-within-groups-in-a-data-frame)*

  ```R
  library(dplyr)
  base_cord_test <- base_cord_test %>% group_by(DISTCD) %>% mutate(id = row_number())
  ```
  
  and we can get the result table like this:
  ```R
  # A tibble: 297,681 x 4
  # Groups:   DISTCD [542]
   DISTCD   lat  long    id
    <int> <dbl> <dbl> <int>
 1    999  2.32 -58.3     1
 2    999  6.02 -58.3     2
 3    999  5.57 -59.5     3
 4     18  5.36 -58.5     1
 5    532  5.21 -58.1     1
 6     33  3.46 -57.6     1
 7     32  4.39 -57.5     1
 8     24  4.47 -58.5     1
 9    999  4.19 -57.6     4
10    999  3.23 -57.5     5
# ... with 297,671 more rows
  ```
  
   
## Remove rows or columns in dataset
*(Reference: https://stackoverflow.com/questions/4862178/remove-rows-with-all-or-some-nas-missing-values-in-data-frame)*
   Needs package `dplyr`.
   
   We can use `select` to remove columns, use `subset` to remove rows.
   
   ```R
   # remove column
   PRISM_all_states_wide <- select(PRISM_all_states_wide, -date)
   quarterly_climate_clean <- subset(quarterly_climate_clean, select = -c(qdate, X1))
   
   # remove column by index number
   southern_planting_reg <- southern_planting_reg[-c(16:162)]
   
   # remove rows based on value (keep subset of year>1960)
   PRISM_all_states_wide <- subset(PRISM_all_states_wide, year > 1960)
   
   # remove rows using logic statement (e.g. remove year 1976 and 2020)
   tms_pine <- tms_pine[!(tms_pine$year == 1976 | tms_pine$year == 2020),]   # That comma is important
   
   # remove rows with missing values (NA)
   # We can use command complete.cases
   PRISM_all_states <- PRISM_all_states[complete.cases(PRISM_all_states), ]
   ```
   

## Remove or replace a character from a variable
  Use command `sub`.
  
  Suppose we have a dataset as below:
  
  ```
    fips     year  value
  1 1001    X1970 40.464
  2 1003    X1970 29.294
  3 1005    X1970 24.921
  4 1007    X1970 22.082
  5 1009    X1970 41.271
  6 1011    X1970 18.885
  ```
  
  If we want to remove the character "X" in variable `year`, we can use the command below:
  
  ```R
  pop_usa_long$year <- sub("X", "", pop_usa_long$year)
  ```
  
  It works by replacing character "X" by nothing. If we want to replace "X" by something else, we can still apply this command.

## Rename a variable
   *(Reference: http://rprogramming.net/rename-columns-in-r/)*
   
   Here is an example of renaming a variable:
   ```R
   # Rename a column in R
   colnames(data)[colnames(data)=="old_name"] <- "new_name"
   ```
   
   There are more examples in the reference page.
   
   Or using command `setnames` in `data.table` package:
   ```R
   setnames(South_crop_yield, old = c('CORN', 'COTTON', 'SOYBEANS'), new = c('yield.CORN', 'yield.COTTON', 'yield.SOYBEANS'))
   ```
   
## reshape2 package
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

   We can use command  `reshape` to convert multiple value variables, for example, a dataset named `temp`:
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
   Need to notice that if multiple rows meets the requirement, the first row will be picked. So if you want to pick the average, you should calculate it first (see *Average per group*)
   
## Split a variable
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
   

## Slide data (Create lagged variables)
   Create lagged data (mostly time):
   ```R
   slide(data, Var, TimeVar, GroupVar, NewVar, slideBy = -1,
         keepInvalid = FALSE, reminder = TRUE)
   ```
   In real situation application:
   ```R
   Climate_quartly_NC <- slide(Climate_quartly_NC, Var = "ppt.1", 
                               TimeVar = "year", GroupVar = "COUNTYCD", NewVar = "ppt.1.lag1", slideBy = -1)
   ```
   This script creates a one-year lag variable of ppt.1 in dataset Climate_quartly_NC.
   
## Subsetting data
   *(Reference: https://www.r-bloggers.com/5-ways-to-subset-a-data-frame-in-r/)*
   
   Subsetting data by values in variables:
   ```R
   ed_exp4 <- subset(education, Region == 2, select = c("State","Minor.Population","Education.Expenditures"))
   ```

## Switch column sequence (reorder dataframe)
   *(Reference: https://stackoverflow.com/questions/5620885/how-does-one-reorder-columns-in-a-data-frame)*
   
   Just simply reorder by column number.
   
   ```R
   df <- df[, c(5,1,2,3,4,6,7,8,9)]
   ```
