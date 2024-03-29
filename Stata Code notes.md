# Stata codes

## bysort
   `bysort` command sort the dataset by its following variables, then execute following commands by sorted groups.

   For example:
   ```Stata
   bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)  
   *create variable of mean planted data, group by countycd and planted year
   
   bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
   ```
   
   
## Create lag variable
   *(Reference: https://www.stata.com/support/faqs/data-management/creating-lagged-variables/)*
   
   Create lag (or lead) variables using subscripts.
   
   ```Stata
   gen lag1 = x[_n-1]
   gen lag2 = x[_n-2]
   gen lead1 = x[_n+1]
   ```
	
   You can create lag (or lead) variables for different subgroups using the by prefix. For example,
  
   ```Stata
   sort state year 
   by state: gen lag1 = x[_n-1]
   ```
	
   If there are gaps in your records and you only want to lag successive years, you can specify
   
   ```Stata
   sort state year
   by state: gen lag1 = x[_n-1] if year==year[_n-1]+1
   ```

## Create percentage summaries 
   *(Reference: https://www.stata.com/support/faqs/data-management/creating-percent-summary-variables/)*
   
   Use `tabulate`
   
   ```Stata
   tabulate year annual_change_val if (concession_type != 0)
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
   estpost sum [varlist]  // a table with various summary list
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

### Export summary statistics as latex file

Use package `sutex`

```stata
sutex plantAcre pnw_price_ton tms_price annual_ppt annual_tmean oil_real_price ///
annual_int_rate Revenue_CORN Revenue_SOYBEANS, lab nobs key(descstat) replace ///
file(descstat.tex) title("Summary statistics")

```

## Generate quartly date according to monthly date
   ```Stata
   gen qdate = qofd(dofm(ym(year,month)))
   format %tq qdate
   ```
   
   `qdate` will be exported as character when dataset being exported as .csv to R

## Generate variables using if
   ```Stata
   gen Tstage = 0
   replace Tstage = 1 if (year > 1997 & year < 2009)
   replace Tstage = 2 if (year >= 2009)
   ```

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

## Predict after regression

```Stata
reg y x1 x2 x3
predict y_prdt   
* y_prdt is the name of variable
```

## Show the number of unique values in variable
   ```Stata
   unique var
   ```
   
   
## String match
(*Reference: http://wlm.userweb.mwn.de/Stata/wstavart.htm*)   

   Use `strmatch(s1, s2)` if we want a statement about a string variable. `s1` is a variable or character we want to match, `s2` is the pattern. It will return 1 if the variable matches the pattern, 0 if not.

   ```Stata
   gen treatStatus = 0
   replace treatStatus = 1 if strmatch(UID, "mtoverlap*") == 1
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
   

