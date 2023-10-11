
# Post \#73138172 [Link](https://stackoverflow.com/questions/73138172/)

## Concatenate every two columns in dataframe

**Vote**: 11 (370/702) **Views**: 691 (684/702) 

**Internal ID** \#1-4-315

Created at 2022-07-27 12:44:27

Tags: `r`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I have the following data frame:
```
X1   X2   X3   X4   X5   X6   X7
p1   H    I    K    J    K    H
p2   H    K    J    K    I    J
p3   J    K    H    I    J    K
p4   K    I    H    J    I    J
```

I want to create a new data frame with the column `X1` and concatenate every two columns starting from `X2` so the final table looks like:
```
X1   X2    X3    X4   
p1   HI    KJ    KH
p2   HK    JK    IJ
p3   JK    HI    JK
p4   KI    HJ    IJ
```



----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2022-07-27 13:54:14

------------

Using `mapply`:
```
cbind(df[ 1 ],
      mapply(paste0, df[, seq(2, 7, 2)], df[, seq(3, 7, 2)]))
#   X1 X2 X4 X6
# 1 p1 HI KJ KH
# 2 p2 HK JK IJ
# 3 p3 JK HI JK
# 4 p4 KI HJ IJ
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-07-27 12:53:19

------------

Base R way
```
df=structure(list(X1 = c("p1", "p2", "p3", "p4"), X2 = c("H", "H", 
"J", "K"), X3 = c("I", "K", "K", "I"), X4 = c("K", "J", "H", 
"H"), X5 = c("J", "K", "I", "J"), X6 = c("K", "I", "J", "I"), 
    X7 = c("H", "J", "K", "J")), class = "data.frame", row.names = c(NA, 
-4L))

df1=data.frame(t(df))
df1$G=c(0,rep(1:((nrow(df1)-1)/2),each=2))

data.frame(
  t(
    aggregate(
      .~G,
      data=df1,
      paste0,
      collapse=""
    )[,-1]
  )
)
```

resulting in
```
X1 X2 X3 X4
X1 p1 HI KJ KH
X2 p2 HK JK IJ
X3 p3 JK HI JK
X4 p4 KI HJ IJ
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-07-27 12:55:43

------------

While `tidyr`'s `unite` is good for small datasets:
```
library(tidyr)

df |>
  unite("X2", X2:X3, sep = "") |>
  unite("X4", X4:X5, sep = "") |>
  unite("X6", X6:X7, sep = "")
```

.. we might want to explore another way for general approach. One such is to pivot to a longer format, change all odd numbered columns to the preceeding even number (using the modulo operator) and then pivot longer collapsing the strings with paste0.
```
library(tidyr)
library(dplyr)

df |>
    pivot_longer(-X1,
                 names_prefix = "X",
                 names_transform = as.numeric) |>
    mutate(name = if_else(name %% 2 == 1, name - 1, name)) |>
    pivot_wider(names_from = name,
                names_prefix = "X",
                values_fn = ~ paste0(., collapse = ""))
```

Output:
```
# A tibble: 4 × 4
  X1    X2    X4    X6   
  <chr> <chr> <chr> <chr>
1 p1    HI    KJ    KH   
2 p2    HK    JK    IJ   
3 p3    JK    HI    JK   
4 p4    KI    HJ    IJ
```

Data:
```
library(readr)

df <- read_table("X1   X2   X3   X4   X5   X6   X7
p1   H    I    K    J    K    H
p2   H    K    J    K    I    J
p3   J    K    H    I    J    K
p4   K    I    H    J    I    J")
```


If we want to start from `X3` instead you'll need to change the code in two places. First, by not pivoting two columns (`-c(X1, X2)`) and then by subtracting 1 from the even columns instead (`name %% 2 == 0`). E.g.
```
library(tidyr)
library(dplyr)

df |>
    pivot_longer(-c(X1, X2),
                 names_prefix = "X",
                 names_transform = as.numeric) |>
    mutate(name = if_else(name %% 2 == 0, name - 1, name)) |>
    pivot_wider(names_from = name,
                names_prefix = "X",
                values_fn = ~ paste0(., collapse = ""))
```

Output:
```
# A tibble: 4 × 5
  X1    X2    X3    X5    X7   
  <chr> <chr> <chr> <chr> <chr>
1 p1    H     IK    JK    H    
2 p2    H     KJ    KI    J    
3 p3    J     KH    IJ    K    
4 p4    K     IH    JI    J
```

(There is of course no X8 to combine with here.)


------------
    
    
## Answer \#3

 Vote: 4

Created at 2022-07-27 13:41:24

------------

A `base` solution:
```
df2 <- df[-1]
cbind(df[1],
  lapply(
    split(as.list(df2), paste0('V', ceiling(1:ncol(df2) / 2))),
    do.call, what = paste0
  )
)

#   X1 V1 V2 V3
# 1 p1 HI KJ KH
# 2 p2 HK JK IJ
# 3 p3 JK HI JK
# 4 p4 KI HJ IJ
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-07-27 13:26:36

------------

An option with `dplyr`:
```
df %>%
 transmute(X1,
           across(c(seq(2, length(.), 2)), 
                  ~ paste0(., get(names(cur_data())[match(cur_column(), names(cur_data())) + 1])))) %>%
 rename_with(~ paste0("X", seq_along(.)), everything())

  X1 X2 X3 X4
1 p1 HI KJ KH
2 p2 HK JK IJ
3 p3 JK HI JK
4 p4 KI HJ IJ
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2022-07-27 14:10:26

------------

A general function in Base R:
```
df <- data.frame(
  X1 = c("p1", "p2", "p3", "p4"),
  X2 = c("H", "H", "J", "K"),
  X3 = c("I", "K", "K", "I"),
  X4 = c("K", "J", "H", "H"),
  X5 = c("J", "K", "I", "J"),
  X6 = c("K", "I", "J", "I"), 
  X7 = c("H", "J", "K", "J")
)

catcols <- function(df, start = 1, by = 2) {
  start1 <- start - 1
  by1 <- by - 1
  n <- ncol(df)
  setNames(
    cbind(
      cbind(
        df[, seq_len(start1)],
        mapply(
          function(i) do.call(paste0, df[,i:(i + by1)]),
          seq(start, n - by1, by)
        )
      ),
      df[, c(0, (n:1)[(n - start1) %% by])]
    ),
    names(df)[1:(ceiling((n - start1)/by) + start1)]
  )
}

catcols(df, 2)
#>   X1 X2 X3 X4
#> 1 p1 HI KJ KH
#> 2 p2 HK JK IJ
#> 3 p3 JK HI JK
#> 4 p4 KI HJ IJ
catcols(df, 3)
#>   X1 X2 X3 X4 X5
#> 1 p1  H IK JK  H
#> 2 p2  H KJ KI  J
#> 3 p3  J KH IJ  K
#> 4 p4  K IH JI  J
catcols(df)
#>    X1 X2 X3 X4
#> 1 p1H IK JK  H
#> 2 p2H KJ KI  J
#> 3 p3J KH IJ  K
#> 4 p4K IH JI  J
catcols(df, 2, 3)
#>   X1  X2  X3
#> 1 p1 HIK JKH
#> 2 p2 HKJ KIJ
#> 3 p3 JKH IJK
#> 4 p4 KIH JIJ
```



------------
    
    