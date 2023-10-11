
# Post \#74489132 [Link](https://stackoverflow.com/questions/74489132/)

## Remove comma inside quotes

**Vote**: 7 (449/702) **Views**: 339 (701/702) 

**Internal ID** \#1-4-326

Created at 2022-11-18 11:41:48

Tags: `r`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I have strings like:
```
string <- "1, 2, \"something, else\""
```

I want to use `tidyr::separate_rows()` with `sep==","`, but the comma inside the quoted portion of the string is tripping me up. I'd like to remove the comma between something and else (but only this comma).
Here's a more complex toy example:
```
string <- c("1, 2, \"something, else\"", "3, 5, \"more, more, more\"", "6, \"commas, are fun\", \"no, they are not\"")

string
#[1] "1, 2, \"something, else\""                   
#[2] "3, 5, \"more, more, more\""                  
#[3] "6, \"commas, are fun\", \"no, they are not\""
```

I want to get rid of all commas inside the embedded quotations. Desired output:
```
[1] "1, 2, \"something else\""                  
[2] "3, 5, \"more more more\""                  
[3] "6, \"commas are fun\", \"no they are not\""
```



----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2022-11-18 12:47:23

------------

You can define a small function to do the replacement.
```
library(stringr)

rmcom <- function(x) gsub(",", "", x)

str_replace_all(string, "(\"[[:alnum:]]+,[ [:alnum:],]*\")", rmcom)
[1] "1, 2, \"something else\""
[2] "3, 5, \"more more more\""
[3] "6, \"commas are fun\", \"no they are not\""
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-11-18 12:36:36

------------

Best I can do:
```
stringr::str_replace_all(string,"(?<=\\\".{1,15})(,)(?=.+?\\\")","")
```

it's:
`(?<= )` = look behind
`\\\"`  = a `\` and a `"`
`.{1,15}` = between 1 and 15 characters (see note)
`(,)` = the comma is what we want to target
`(?= )` look ahead
`.+?` = one or more characters but as few as possible
`\\\"`  = a `\` and a `"`
note: look behind cannot be unbounded, so we can't use `.+?` here.  Adjust the max of 15 for your dataset.
edit: Andre Wildberg's solution is better - I stupidly forgot that the "" defining the string are not part of the string, so made it much more complex than it needed to be.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-11-18 13:12:11

------------

Altenatively, we could invert the problem (and keep the comma, which might be useful) and use a regex directly with `separate_rows` to split only at the comma NOT inside quotes:
```
library(tidyr)

df |>
  separate_rows(stringcol, sep = '(?!\\B"[^\"]*), (?![^"]*\"\\B)')
```

Regex expression from: [Regex find comma not inside quotes](https://stackoverflow.com/questions/21105360/regex-find-comma-not-inside-quotes)
Alternatively: [Regex to pick characters outside of pair of quotes](https://stackoverflow.com/questions/632475/regex-to-pick-characters-outside-of-pair-of-quotes/25544437#25544437)

```
# A tibble: 9 × 1
  stringcol             
  <chr>                 
1 "1"                   
2 "2"                   
3 "\"something, else\"" 
4 "3"                   
5 "5"                   
6 "\"more, more, more\""
7 "6"                   
8 "\"commas, are fun\"" 
9 "\"no, they are not\""
```


```
library(tibble)

df <- tibble(stringcol = string)
```



------------
    
    