
# Post \#73279829 [Link](https://stackoverflow.com/questions/73279829/)

## How to split a comma and colon separated column into respective columns in R?

**Vote**: 2 (631/702) **Views**: 442 (699/702) 

**Internal ID** \#1-4-324

Created at 2022-08-08 14:50:37

Tags: `r` `string` `dataframe` `strsplit`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `python` `r`)

----------

**Notepad**


----------

Say for example I have a column that looks something like:
```
name:Michael,Age:31,City:NYC
```

How could I split this column into separate columns such that it would yield a result similar as a data frame to:
```
name   | Age | City
1 Michael | 31  | NYC
```

I've attempted to use mtabulate from the qdapTools package but it only resulted with boolean value columns.


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-08-08 14:58:18

------------

In `base R`, it can be done with `read.dcf`
```
out <- type.convert(as.data.frame( 
         read.dcf(textConnection(paste(gsub(",", "\n", df1$col1),
        collapse = "\n\n")))
), as.is = TRUE)
```

-output
```
> out
     name Age City
1 Michael  31  NYC
2 Michael  31  NYC
```


---


Or using `tidyverse`
```
library(dplyr)
library(tidyr)
df1 %>%
   mutate(rn = row_number()) %>%
   separate_rows(col1, sep = ",\\s*") %>% 
   separate(col1, into = c('col1', 'col2'), sep = ":") %>% 
   pivot_wider(names_from = col1, values_from = col2) %>% 
   select(-rn)
# A tibble: 2 × 3
  name    Age   City 
  <chr>   <chr> <chr>
1 Michael 31    NYC  
2 Michael 31    NYC
```


### data


```
df1 <- structure(list(col1 = c("name:Michael,Age:31,City:NYC",
 "name:Michael,Age:31,City:NYC"
)), class = "data.frame", row.names = c(NA, -2L))
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-08-08 14:59:45

------------

We can use `str_split_fixed` from the `stringr` package:
```
library(stringr)

df[c("name", "Age", "City")] <- str_split_fixed(
    gsub("\\w+:", "", df$col), ",", 3
)
```


```
df <- data.frame(col=c("name:Michael,Age:31,City:NYC"),
                 stringsAsFactors=FALSE)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-08 15:02:52

------------

We can `separate` your single column dataframe into three columns, then remove the texts before colon `:`.
```
library(dplyr)
library(tidyr)

df %>% 
  separate(col, into = c("name", "Age", "city"), sep = ",") %>% 
  mutate(across(everything(), ~gsub("^.+?:", "", .x)))

     name Age city
1 Michael  31  NYC
```


### Data


```
df <- structure(list(col = "name:Michael,Age:31,City:NYC"), class = "data.frame", row.names = c(NA, 
-1L))
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-08-08 15:05:40

------------

```
library(tidyverse)

data <- "name:Michael,Age:31,City:NYC"

data |>
  tibble() |>
  separate_rows(data, sep = ",") |>
  separate(data, into = c("name", "value")) |>
  pivot_wider(names_from = "name", values_from = "value")

#> # A tibble: 1 × 3
#>   name    Age   City 
#>   <chr>   <chr> <chr>
#> 1 Michael 31    NYC
```

[reprex package](https://reprex.tidyverse.org)


------------
    
    