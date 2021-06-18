# Stata codes

## bysort
   `bysort` command sort the dataset by its following variables, then execute following commands by sorted groups.

   For example:
   ```Stata
   bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)  
   *create variable of mean planted data, group by countycd and planted year
   
   bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
   ```
   
## esttab package

   To install `esttab`, use `ssc install estout`

   `esttab` produces publication-style tables that display nicely in Stata's results window. The basic syntax of esttab is:
   ```Stata
   esttab [ namelist ] [ using filename ] [ , options estout_options ]
   ```
   
   ### Generate summary table
   *(Reference: http://repec.sowi.unibe.ch/stata/estout/esttab.html)*
   
   ```Stata
   estpost summary [varlist]  // a table with various summary list
   esttab, cell((mean sd))    // can also choose other factors
   
   eststo clear
   ```
   
   For now, I use online converter to export the latex syntax.
   
   ### Generate regression result table
   
   ```Stata
   eststo: quietly reg [varlist1]
   eststo: quietly reg [varlist2]
   
   esttab using table.tex, replace wide  
   ```
   
   This will create a table with two regression results.
   
## Export data

### Export data to csv file

```stata
outsheet  year statecd tmscd countycd avg_area tms_price p_pnw  using south_loblolly_plt.csv, comma

```

### Export regression results as latex file

Use package `estout`

First, use `eststo` to store regression results 

```stata
est clear
eststo: quietly reg avg_area time_stage_3rd#c.p_pnw time_stage_3rd#c.tms_price p_pnw tms_price /*
*/ revn_CORN revn_SOYBEAN i.time_stage_3rd i.countycd

eststo: quietly rreg avg_area time_stage_3rd#c.p_pnw time_stage_3rd#c.tms_price p_pnw tms_price /*
*/ revn_CORN revn_SOYBEAN i.time_stage_3rd i.countycd
```

Then, use `esttab`to get the LaTeX output

```stata
esttab using"table1.tex", replace ///
 cells("b(fmt(3) star label(Coef.)) se(par fmt(2) label(Std. err.))") ///
 nomtitle label star(* 0.10 ** 0.05 *** 0.01)
```

For more information, go to `help esttab`.

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
   
## Loop

### Generate time dummy variables with loop
```Stata
forvalues i = 1970/2016 {
	generate D`i' = 1 if (year > `i')
	replace D`i' = 0 if !(year  > `i')
	}
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
   

