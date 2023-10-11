
# Post \#68760442 [Link](https://stackoverflow.com/questions/68760442/)

## Relocate rows with tidyverse

**Vote**: 9 (409/702) **Views**: 2095 (602/702) 

**Internal ID** \#1-4-332

Created at 2021-08-12 15:40:11

Tags: `r` `dplyr` `tidyverse` `tidyr` `relocate`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

Is it possible to `relocate`  in `tidyverse` framework like it is possible for columns with `dplyr` `relocate`?
In this example I would like to relocate row 1 to position 5 (end of dataframe)
My dataframe:
```
df <- structure(list(ID = c(1, 2, 3, 4, 5), var1 = c("a", "b", "c", 
"d", "e"), var2 = c(1, 1, 0, 0, 1)), class = "data.frame", row.names = c(NA, 
-5L))

df
  ID var1 var2
1  1    a    1
2  2    b    1
3  3    c    0
4  4    d    0
5  5    e    1
```

Desired output:
```
ID var1 var2
1  2    b    1
2  3    c    0
3  4    d    0
4  5    e    1
5  1    a    1
```

Note: In the it should be 'pipe friendly' solution. I tried a lot but found nothing. Thank you.


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-08-12 16:19:45

------------

`arrange()` is the tidyverse verb for reordering rows. It can be (ab)used as follows:
```
dplyr::arrange(df, ID==1)
```

(`ID==1` is logical; when it is ordered `FALSE` values come before `TRUE` values ...)
This isn't as flexible as `relocate()` (e.g. it's not immediately obvious how to say "move rows 100-200 so they are immediately after row 1000"), but you can probably find a way to do most tasks.
Another option (less idiomatic in my opinion) is `slice()`:
```
dplyr::slice(df, order(ID==1))
```

(this is a tidyverse translation of @akrun's base-R answer). Either of these solutions can also be written with pipes (e.g. `df %>% arrange(ID==1)`).
Just to be silly:
```
df %>% `[`(order(.$ID==1),)
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-08-12 17:12:42

------------

Using `base R`
```
df[order(df$ID == 1), ]
```


---


Or with `slice`
```
library(dplyr)
df %>%
    slice(2:n(), 1)
```

Or specify with `row_number()`
```
df %>% 
   slice(lead(row_number(), default = 1))
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-08-12 15:46:16

------------

Maybe this is not so elegant but here is a way:
```
library(dplyr)

df %>%
  filter(between(row_number(), 2, nrow(df))) %>%
  bind_rows(df[1, ])

  ID var1 var2
1  2    b    1
2  3    c    0
3  4    d    0
4  5    e    1
5  1    a    1
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2021-08-12 20:28:43

------------

Let's play a math trick
```
> df[order((seq(nrow(df)) -2) %% nrow(df)), ]
  ID var1 var2
2  2    b    1
3  3    c    0
4  4    d    0
5  5    e    1
1  1    a    1
```

or
```
> df %>%
+   arrange(replace(row_number(), 1, n() + 1))
  ID var1 var2
1  2    b    1
2  3    c    0
3  4    d    0
4  5    e    1
5  1    a    1
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-08-12 20:59:38

------------

After the whole day of trial and error and your wonderful answers:
```
library(dplyr)

df %>% 
    slice(-1) %>% bind_rows(df %>% slice(1))
```

Output:
```
ID var1 var2
1  2    b    1
2  3    c    0
3  4    d    0
4  5    e    1
5  1    a    1
```



------------
    
    