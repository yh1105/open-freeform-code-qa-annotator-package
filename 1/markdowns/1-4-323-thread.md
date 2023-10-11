
# Post \#70691645 [Link](https://stackoverflow.com/questions/70691645/)

## Extract all row.names in a data.frame that match a value in another data.frame

**Vote**: 0 (696/702) **Views**: 979 (659/702) 

**Internal ID** \#1-4-323

Created at 2022-01-13 05:01:38

Tags: `r` `dataframe` `rows`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `python` `r`)

----------

**Notepad**


----------

I have a data.frame with 150 column names. For each column, I want to extract the maximum and minimum values (the rows repeat) and the row names of each maximum value. I have extracted the min and max values in another data.frame but don't know how to match them.
I have found functions that are very close for this, like for minimum values:
```
head(cars)
  speed dist
1     4    2
2     4   10
3     7    4
4     7   22
5     8   16
6     9   10

sapply(cars,which.min)

speed  dist 
    1     1
```

Here, it only gives the first index for minimum speed.
And I've tried with loops like:
```
for (i in (colnames(cars))){
  print(min(cars[[i]]))
}


[1] 4
[1] 2
```

But that just gives me the minimum values, and not if they are repeated and the rowname of each repeated value.
I want something like:
```
min.value  column  rowname   freq.times
4          speed      1,2        2
2          dist       1          1
```

Thanks and sorry if I have orthography mistakes. No native speaker


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-01-13 06:09:21

------------

One option is to use `tidyverse`. I was a little unclear if you want `min` and `max` in the same dataframe, so I included both. First, I create an index column with row numbers. Then, I pivot to long format to determine which values are minimum and maximum (using `case_when`). Then, I drop the rows that are not min or max (i.e., `NA` in category). Then, I use `summarise` to turn the row names into  a single character string and get the frequency of a given minimum or maximum value.
```
library(tidyverse)

cars %>%
  mutate(rowname = row_number()) %>%
  pivot_longer(-rowname, names_to = "column", values_to = "value") %>%
  group_by(column) %>%
  mutate(category = case_when((value == min(value)) == TRUE ~ "min",
                              (value == max(value)) == TRUE ~ "max")) %>%
  drop_na(category) %>%
  group_by(column, value, category) %>%
  summarise(rowname = toString(rowname), freq.times = n()) %>% 
  select(2:3, 1, 4, 5)
```


```
# A tibble: 4 × 5
# Groups:   column, value [4]
  value category column rowname freq.times
  <dbl> <chr>    <chr>  <chr>        <int>
1     2 min      dist   1                1
2   120 max      dist   49               1
3     4 min      speed  1, 2             2
4    25 max      speed  50               1
```

However, if you want to produce the dataframes separately. Then, you could adjust something like this. Here, I don't use `category` and instead use `filter` to drop all rows that are not the minimum for a group/column. Then, we can `summarise` as we did above. You can do the samething for `max` as well.
```
cars %>%
  mutate(rowname = row_number()) %>%
  pivot_longer(-rowname, names_to = "column", values_to = "min.value") %>%
  group_by(column) %>%
  filter(min.value == min(min.value)) %>%
  group_by(column, min.value) %>%
  summarise(rowname = toString(rowname), freq.times = n()) %>% 
  select(2, 1, 3, 4)
```

Output
```
# A tibble: 2 × 4
# Groups:   column [2]
  min.value column rowname freq.times
      <dbl> <chr>  <chr>        <int>
1         2 dist   1                1
2         4 speed  1, 2             2
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-01-13 08:03:25

------------

Here is another `tidyverse` approach:
`which.min(.)` gives the first index, whereas `which(. == min(.))` will give all indices that are true for the condition!
Analogues to get the frequence we could use: `length(which(.==min(.)))`

1. summarise across all columns min.value, rowname and freq.time
2. The part after is pivoting to bring the column name in position.


```
library(tidyverse)

cars %>% 
  summarise(across(dplyr::everything(), list(min.value = min,
                                             rowname = ~list(which(. == min(.))),
                                             freq.times = ~length(which(.==min(.)))))) %>% 
  pivot_longer(
    cols = contains("_"),
    names_to = "key",
    values_to = "val", 
    values_transform = list(val = as.character)
  ) %>% 
  separate(key, c("column", "name"), sep="_") %>% 
  pivot_wider(
    names_from = name,
    values_from = val
  ) %>% 
  mutate(rowname = str_replace(rowname, '\\:', '\\,'))
```

```
column min.value rowname freq.times
  <chr>  <chr>     <chr>   <chr>     
1 speed  4         1,2     2         
2 dist   2         1       1
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-01-13 05:40:04

------------

```
min.value <- sapply(cars, min)
columns <- names(min.value)
row.values <- sapply(columns, \(x) which(cars[[x]] == min.value[which(names(min.value) == x)]))
freq.times <- sapply(row.values, length)
row.values <- sapply(row.values, \(x) paste(x, collapse = ","))
names(min.value) <- names(row.values) <- names(freq.times) <- NULL

data.frame(min.value = min.value,
           columns = columns,
           row.values = row.values, 
           freq.times = freq.times)

  min.value columns row.values freq.times
1         4   speed        1,2          2
2         2    dist          1          1
```

Here it is wrapped in function, so that you can use it across whatever data frame and function you need:
```
create_table <- function(df, FUN) {
  values <- sapply(df, FUN)
  columns <- names(values)
  row.values <- sapply(columns, \(x) which(df[[x]] == values[which(names(values) == x)]))
  freq.times <- sapply(row.values, length)
  row.values <- sapply(row.values, \(x) paste(x, collapse = ","))
  names(values) <- names(row.values) <- names(freq.times) <- NULL
  
  data.frame(values = values,
             columns = columns,
             row.values = row.values, 
             freq.times = freq.times)
}

create_table(cars, min)
  values columns row.values freq.times
1      4   speed        1,2          2
2      2    dist          1          1

create_table(cars, max)
  values columns row.values freq.times
1     25   speed         50          1
2    120    dist         49          1
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-01-13 06:42:07

------------

You can use `which` to obtain the positions. `sapply` should work. Since you need multiple summary statistics for each column, you just have to wrap up them in a list. Something like this
```
as.data.frame(sapply(cars, \(x) {
  extrema <- range(x)
  min.row <- which(x == extrema[[1L]])
  max.row <- which(x == extrema[[2L]])
  list(
    min.value = extrema[[1L]], max.value = extrema[[2L]],
    min.row = min.row, max.row = max.row, 
    freq.min = length(min.row), freq.max = length(max.row)
  )
}))
```

Output
```
speed dist
min.value     4    2
max.value    25  120
min.row    1, 2    1
max.row      50   49
freq.min      2    1
freq.max      1    1
```



------------
    
    