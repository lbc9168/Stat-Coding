# Stata codes

## bysort
   `bysort` command sort the dataset by its following variables, then execute following commands by sorted groups.

   For example:
   ```Stata
   bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)
   bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
   ```
   
## esttab package

   To install `esttab`, use `ssc install estout`

   `esttab` produces publication-style tables that display nicely in Stata's results window. The basic syntax of esttab is:
   ```Stata
   esttab [ namelist ] [ using filename ] [ , options estout_options ]
   ```
   
   ### Generate summary table
   
   ```Stata
   estpost summary [varlist]  * a table with various summary list
   esttab, cell((mean sd))    * can also choose other factors
   
   eststo clear
   ```
   
   For now, I use online converter to export the latex syntax.

## Generate quartly date according to monthly date
   ```Stata
   gen qdate = qofd(dofm(ym(year,month)))
   format %tq qdate
   ```
   
   `qdate` will be exported as character when dataset being exported as .csv to R

## Install package
   ```Stata
   ssc inst package
   ```

## Show the number of unique values in variable
   ```Stata
   unique var
   ```

## Subset a string
   The following code cuts a variable's subset if the subset is a string.
   ```Stata
   Replace testID = substr(testID, 2, length(testID) - 1))
   ```
   You should be noted that the first position of a string in Stata is 1 instead of 0 in other languages.
   
   A `if` command can be added to commit a restriction:
   ```Stata
   Replace testID = substr(testID, 2, length(testID) - 1)) if substr(testID, length(testID)-4, 5) == " DEAD"
   ```
   Type `help substr()` in Stata for more information.
   

