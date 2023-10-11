
# Post \#61367186 [Link](https://stackoverflow.com/questions/61367186/)

## pivot_longer into multiple columns

**Vote**: 21 (256/702) **Views**: 21226 (291/702) 

**Internal ID** \#1-4-328

Created at 2020-04-22 14:08:48

Tags: `r` `regex` `pivot` `tidyverse` `tidyr`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r` `regex`)

----------

**Notepad**


----------

I am trying to use `pivot_longer`. However, I am not sure how to use `names_sep` or `names_pattern` to solve this.
```
dat <- tribble(
     ~group,  ~BP,  ~HS,  ~BB, ~lowerBP, ~upperBP, ~lowerHS, ~upperHS, ~lowerBB, ~upperBB,
        "1", 0.51, 0.15, 0.05,     0.16,     0.18,      0.5,     0.52,     0.14,     0.16,
      "2.1", 0.67, 0.09, 0.06,     0.09,     0.11,     0.66,     0.68,     0.08,      0.1,
      "2.2", 0.36, 0.13, 0.07,     0.12,     0.15,     0.34,     0.38,     0.12,     0.14,
      "2.3", 0.09, 0.17, 0.09,     0.13,     0.16,     0.08,     0.11,     0.15,     0.18,
      "2.4", 0.68, 0.12, 0.07,     0.12,     0.14,     0.66,     0.69,     0.11,     0.13,
        "3", 0.53, 0.15, 0.06,     0.14,     0.16,     0.52,     0.53,     0.15,     0.16)
```

Desired output (First row from wide data)
```
group names   values lower upper
   1    BP      0.51  0.16  0.18
   1    HS      0.15  0.5   0.52
   1    BB      0.05  0.14  0.16
```



----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2020-04-22 16:05:46

------------

Here is solution following a similar method that @Fnguyen used but using the newer `pivot_longer` and `pivot_wider` construct:

```
library(dplyr)
library(tidyr)

longer<-pivot_longer(dat, cols=-1, names_pattern = "(.*)(..)$", names_to = c("limit", "name")) %>% 
     mutate(limit=ifelse(limit=="", "value", limit))

answer <-pivot_wider(longer, id_cols = c(group, name), names_from = limit, values_from = value, names_repair = "check_unique")
```


Most of the selecting, separating, mutating and renaming is taking place within the pivot function calls.


This regular expressions "(.*)(..)$" means:
  ( ) ( ) Look for two parts,
  (.*) the first part should have zero or more characters
  (..) the second part  should have just 2 characters at the “$” end of the string


------------
    
    
## Answer \#1

 Vote: 6

Created at 2020-04-22 16:03:44

------------

A data.table version (not sure yet how to retain the original names so that you dont need to post substitute them [https://github.com/Rdatatable/data.table/issues/2551](https://github.com/Rdatatable/data.table/issues/2551)):  

```
library(data.table)
df <- data.table(dat)
v <- c("BP","HS","BB")
setnames(df, v, paste0("x",v) )

g <- melt(df, id.vars = "group",
     measure.vars = patterns(values = "x" ,
                             lower = "lower",
                             upper = "upper"),
     variable.name = "names")

g[names==1, names := "BP" ]
g[names==2, names := "HS" ]
g[names==3, names := "BB" ]

    group names values lower upper
 1:     1    BP   0.51  0.16  0.18
 2:   2.1    BP   0.67  0.09  0.11
 3:   2.2    BP   0.36  0.12  0.15
 4:   2.3    BP   0.09  0.13  0.16
 5:   2.4    BP   0.68  0.12  0.14
 6:     3    BP   0.53  0.14  0.16
 7:     1    HS   0.15  0.50  0.52
 8:   2.1    HS   0.09  0.66  0.68
 9:   2.2    HS   0.13  0.34  0.38
10:   2.3    HS   0.17  0.08  0.11
11:   2.4    HS   0.12  0.66  0.69
12:     3    HS   0.15  0.52  0.53
13:     1    BB   0.05  0.14  0.16
14:   2.1    BB   0.06  0.08  0.10
15:   2.2    BB   0.07  0.12  0.14
16:   2.3    BB   0.09  0.15  0.18
17:   2.4    BB   0.07  0.11  0.13
18:     3    BB   0.06  0.15  0.16
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2020-04-22 15:24:45

------------

Based on your example data this solution using `dplyr` works for me:

```
library(dplyr)

dat %>%
  gather(key, values,-group) %>%
  mutate(names = gsub("lower","",gsub("upper","",key))) %>%
  separate(key, into = c("key1","key2") ,"[[:upper:]]", perl=T) %>%
  mutate(key1 = case_when(key1 == "" ~ "values", TRUE ~ key1)) %>%
  select(group,names,key1,values) %>%
  rowid_to_column() %>%
  spread(key1,values) %>%
  select(-rowid) %>%
  group_by(group,names) %>%
  summarise_all(mean,na.rm = TRUE)
```



------------
    
    
## Answer \#3

 Vote: 5

Created at 2022-03-03 15:44:08

------------

I'd like to add an alternative `tidyverse` solution drawing from the answer provided by @Dave2e.
Like Dave2e's solution it's a two-step procedure (). Instead of reshaping the data twice, I add the prefix "values" to the columns named "BP", "HS", and "BB" using `rename_with`. This was necessary for getting the column names right when using the `.value` sentinel in the `names_to` argument of `pivot_longer`.
```
library(dplyr)
library(tidyr)

dat %>% 
  rename_with(~sub("^(BP|HS|BB)$", "values\\1", .)) %>%     # add prefix values
  pivot_longer(dat , cols= -1,
               names_pattern = "(.*)(BP|HS|BB)$",
               names_to = c(".value", "names"))
```



------------
    
    