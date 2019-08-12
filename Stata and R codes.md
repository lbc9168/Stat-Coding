# Code notes 

## Stata

`bysort` command sort the dataset by its following variables, then execute following commands by sorted groups.

For example:
```Stata
bysort COUNTYCD Planted_year: egen avg = mean(Planted_area)
bysort COUNTYCD Planted_year: egen sd = sd(Planted_area)
```

## R

`reshape` command works on reshapping data between wide-format and long-format

For example:

Wide-format
```
##      ozone      wind     temp
## 1 23.61538 11.622581 65.54839
## 2 29.44444 10.266667 79.10000
## 3 59.11538  8.941935 83.90323
## 4 59.96154  8.793548 83.96774
```

