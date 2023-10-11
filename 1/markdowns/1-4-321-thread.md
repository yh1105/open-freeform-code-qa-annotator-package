
# Post \#71437352 [Link](https://stackoverflow.com/questions/71437352/)

## How do I unpack tuple format in R?

**Vote**: 9 (409/702) **Views**: 847 (672/702) 

**Internal ID** \#1-4-321

Created at 2022-03-11 10:49:25

Tags: `r` `dataframe` `data.table` `tuples` `reshape`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `python` `r`)

----------

**Notepad**


----------

Here is the dataset.
```
library(data.table)

x <- structure(list(id = c("A", "B" ),
                    segment_stemming = c("[('Brownie', 'Noun'), ('From', 'Josa'), ('Pi', 'Noun')]", 
                                          "[('Dung-caroon-gye', 'Noun'), ('in', 'Josa'), ('innovation', 'Noun')]" )), 
               row.names = c(NA, -2L), 
               class = c("data.table", "data.frame" ))

x
# id                                                     segment_stemming
# 1:  A               [('Brownie', 'Noun'), ('From', 'Josa'), ('Pi', 'Noun')]
# 2:  B [('Dung-caroon-gye', 'Noun'), ('in', 'Josa'), ('innovation', 'Noun')]
```

I would like to split the tuple into rows. Here is my expected outcome.
```
id             segment_stemming
A              ('Brownie', 'Noun')
A              ('From', 'Josa')
A              ('Pi', 'Noun')
B              ('Dung-caroon-gye', 'Noun')
B              ('in', 'Josa')
B              ('innovation', 'Noun')
```

I've searched the tuple format using R but cannot find out any clue to make the outcome.


----------
        
## Answer \#0

**Accepted** Vote: 12

Created at 2022-03-11 11:35:33

------------


# data.table approach


Here is an option using `data.table` + `reticulate`
```
library(reticulate)
library(data.table)
setDT(x)[
  ,
  segment_stemming := gsub("(\\(.*?\\))", '\"\\1\"', segment_stemming)
][
  ,
  lapply(.SD, py_eval),
  id
]
```

which gives
```
id            segment_stemming
1:  A         ('Brownie', 'Noun')
2:  A            ('From', 'Josa')
3:  A              ('Pi', 'Noun')
4:  B ('Dung-caroon-gye', 'Noun')
5:  B              ('in', 'Josa')
6:  B      ('innovation', 'Noun')
```


---


Another `data.table` option using `strsplit` + `trimws` like below
```
library(data.table)
setDT(x)[
  ,
  .(segment_stemming = trimws(
    unlist(strsplit(segment_stemming, "(?<=\\)),\\s+(?=\\()", perl = TRUE)),
    whitespace = "\\[|\\]"
  )),
  id
]
```

gives
```
id            segment_stemming
1:  A         ('Brownie', 'Noun')
2:  A            ('From', 'Josa')
3:  A              ('Pi', 'Noun')
4:  B ('Dung-caroon-gye', 'Noun')
5:  B              ('in', 'Josa')
6:  B      ('innovation', 'Noun')
```


---



# base R


Some base R options should work as well
```
with(
  x,
  setNames(
    rev(
      stack(
        tapply(
          segment_stemming,
          id,
          function(v) {
            trimws(
              unlist(strsplit(v, "(?<=\\)),\\s+(?=\\()", perl = TRUE)),
              whitespace = "\\[|\\]"
            )
          }
        )
      )
    ),
    names(x)
  )
)
```

or
```
with(
  x,
  setNames(
    rev(
      stack(
        setNames(
          regmatches(segment_stemming, gregexpr("\\(.*?\\)", segment_stemming)),
          id
        )
      )
    ),
    names(x)
  )
)
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2022-03-11 11:05:03

------------

Here's a way using `separate_rows`:
```
library(tidyverse)

x %>% 
  mutate(segment_stemming = gsub("\\[|\\]", "", segment_stemming)) %>% 
  separate_rows(segment_stemming, sep = ",\\s*(?![^()]*\\))")

# A tibble: 6 x 2
  id    segment_stemming           
  <chr> <chr>                      
1 A     ('Brownie', 'Noun')        
2 A     ('From', 'Josa')           
3 A     ('Pi', 'Noun')             
4 B     ('Dung-caroon-gye', 'Noun')
5 B     ('in', 'Josa')             
6 B     ('innovation', 'Noun')
```

One way to get a better result, with some manipulation (`unnest_wider` is not necessary).
```
x %>% 
  mutate(segment_stemming = gsub("\\[|\\]", "", segment_stemming)) %>% 
  separate_rows(segment_stemming, sep = ",\\s*(?![^()]*\\))") %>% 
  mutate(segment_stemming = segment_stemming %>% 
           str_remove_all("[()',]") %>% 
           str_split(" ")) %>% 
  unnest_wider(segment_stemming)

# A tibble: 6 x 3
  id    ...1            ...2 
  <chr> <chr>           <chr>
1 A     Brownie         Noun 
2 A     From            Josa 
3 A     Pi              Noun 
4 B     Dung-caroon-gye Noun 
5 B     in              Josa 
6 B     innovation      Noun
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-03-11 11:40:58

------------

Here is another potential option:
```
library(data.table)

dt <- structure(list(id = c("A", "B" ), segement_stemming = c("[('Brownie', 'Noun'), ('From', 'Josa'), ('Pi', 'Noun')]", "[('Dung-caroon-gye', 'Noun'), ('in', 'Josa'), ('innovation', 'Noun')]" )), row.names = c(NA, -2L), class = c("data.table", "data.frame" ))

dt2 <- dt[, c(segement_stemming = strsplit(segement_stemming, "(?<=[^']),", perl = TRUE)), by = id]
dt2[, names(dt2) := lapply(.SD, function(x) gsub("\\[|\\]", "", x))]
dt2
#>    id           segement_stemming
#> 1:  A         ('Brownie', 'Noun')
#> 2:  A            ('From', 'Josa')
#> 3:  A              ('Pi', 'Noun')
#> 4:  B ('Dung-caroon-gye', 'Noun')
#> 5:  B              ('in', 'Josa')
#> 6:  B      ('innovation', 'Noun')
```

[reprex package](https://reprex.tidyverse.org)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-03-11 11:52:30

------------

```
x[,.(segment_stemming = unlist(str_extract_all(segment_stemming, "\\(.*?\\)"))), by = id]
```

or you can use `tidyr::unnest`. This way there is only one call to `str_extract_all`:
```
x[, segment_stemming := str_extract_all(segment_stemming, "\\(.*?\\)")]
unnest(x, segment_stemming)
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-03-11 11:56:38

------------

A data.table way would be such as:
```
library(stringr)

x [, segment_stemming:=gsub("\\[|\\]", "", segment_stemming, perl = T)] #remove brackets
x [, parsed := str_split(segment_stemming, "\\),")]                     # split string
out <- x[, .(unlist(parsed, recursive = F)), by = .(id)]                # unlist elements
out [ , V1  := gsub("\\)?$",")", V1)][]                                 # adjust format

       id                          V1
   <char>                      <char>
1:      A         ('Brownie', 'Noun')
2:      A            ('From', 'Josa')
3:      A              ('Pi', 'Noun')
4:      B ('Dung-caroon-gye', 'Noun')
5:      B              ('in', 'Josa')
6:      B      ('innovation', 'Noun')
```



------------
    
    