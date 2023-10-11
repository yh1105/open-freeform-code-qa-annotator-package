
# Post \#69525690 [Link](https://stackoverflow.com/questions/69525690/)

## remove Rows with complete set of NA

**Vote**: 2 (631/702) **Views**: 1692 (615/702) 

**Internal ID** \#1-4-320

Created at 2021-10-11 11:46:21

Tags: `r` `filter` `subset` `na`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I have a dataset and I would like to delete the rows that have a complete set of NAs in columns 456:555, I want to keep those with some NAs but I need to delete those with a complete set of NAs
I have tried
```
final[complete.cases(final[ , 456:555]),]
```

but this doesn't work. It says
```
Error in help.search(c("[", "final", "complete.cases(final[, c(456:555)])",  : argument ‘pattern’ must be a single character string
```

then I think this probably would work:
```
data[rowSums(is.na(data)) != ncol(data),]
```

but I don't know where to include 456:555 there
what should I do?
Thanks!


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-10-11 20:18:47

------------

We can use dplyr. With the example by @lovalery:
```
library(dplyr)

df %>% filter(!if_all(V2:V3, is.na))

#>   V1 V2 V3
#> 1  3  3 NA
#> 2 NA  1 NA
#> 3  3  5 NA
```

We can use many different selection statements inside `if_all`. Check the documentation for more examples.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-10-11 12:29:04

------------

Maybe you can do something like this, not the cleanest approach:
```
# data frame with one row complete NA
df <- data.frame(V1 = c(NA, 3, NA, 2, 3),
                 V2 = c(NA, 3, 1, NA, 5),
                 V3 = c(NA, NA, NA ,NA, NA))

df

V1 V2 V3
1 NA NA NA
2  3  3 NA
3 NA  1 NA
4  2 NA NA
5  3  5 NA



old_df <- df[4:5,] # get rows you wanna keep regardless of number of NAs
new_df <- df[1:3,] # get rows where you wanna delete complete NAs

# "delete" complete NAs
new_df <-  new_df %>% 
    filter(is.na(new_df) %>% rowSums() != length(new_df))

# build the two dfs together
df <-rbind(old_df, new_df)
df

V1 V2 V3
4  2 NA NA
5  3  5 NA
1  3  3 NA
2 NA  1 NA
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-10-11 13:16:28

------------

Here is one simple solution with the `sjmisc` package:
`df[!apply(df[456:555],1,sjmisc::all_na),]`
To check that it does what you want, please find a little reprex:

```
df <- data.frame(V1 = c(NA, 3, NA, 2, 3),
                 V2 = c(NA, 3, 1, NA, 5),
                 V3 = c(NA, NA, NA ,NA, NA))

df
#>   V1 V2 V3
#> 1 NA NA NA
#> 2  3  3 NA
#> 3 NA  1 NA
#> 4  2 NA NA
#> 5  3  5 NA

# Select all line except `all_na` lines for the selected columns:
df[!apply(df[2:3],1,sjmisc::all_na),]
#>   V1 V2 V3
#> 2  3  3 NA
#> 3 NA  1 NA
#> 5  3  5 NA
```

[reprex package](https://reprex.tidyverse.org)


------------
    
    