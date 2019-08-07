# Code notes 

## Stata

`bysort` command sort the dataset by its following variables, then execute following commands by sorted groups
For example:
```Stata
bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)
bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
```

## R
