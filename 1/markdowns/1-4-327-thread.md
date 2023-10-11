
# Post \#67649621 [Link](https://stackoverflow.com/questions/67649621/)

## data.frame with 2 columns with the same name: how to select the second one?

**Vote**: 2 (631/702) **Views**: 2360 (594/702) 

**Internal ID** \#1-4-327

Created at 2021-05-22 12:46:22

Tags: `r` `dataframe`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `python` `r`)

----------

**Notepad**


----------

```
d1 <- data.frame(a=c(1,2,3))
d2 <- data.frame(a=c(3,4,5))
d3 <- cbind(d1,d2)
```

doesn't return an error, and an inspection of the environment in RStudio displays two columns with the same name.
If I type:
```
d3$a
```

The first column is selected. How to select the second by name?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-05-22 13:28:11

------------

It is not advised to have duplicate column names and this is one of the main reason.
You can select column by position.
```
d3[[2]]
```

Or if you want to select them by name, here is another way -
```
d3[names(d3) == 'a'][[2]]
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-05-22 13:32:20

------------

It is a little surprising that `R` does not throw an `error`/`warning` when you `cbind` 2 `dataframes` that have the same `column` names. Because, usually `R dataframes` do not allow exact same names (when you create them using `data.frame()`).
For example, if you try to create a `dataframe` (from scratch/without binding 2 `dataframes`) `R` automatically changes names slightly by adding `.number` starting from the second `column`:
```
data.frame(a = c(1, 2, 3), a = c(3, 4, 5), a = c(5, 6, 7))

  a a.1 a.2
1 1   3   5
2 2   4   6
3 3   5   7
```

Which means `dataframes` should not have the same `column` names. In your example, when you do `d3$a` `R` can only show you the first `column` that have the name `a`. Similarly, if you do `d3[,"a"]` you get the first `column` with the name `a`.
In general, it would not be a good idea to have a `dataframe` with multiple `columns` with the exact same names. However, if you really need to have such a `dataframe` and you have to get the second `column`, you would need to do something like:
```
d3[, 2]

[1] 3 4 5
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-05-22 13:38:33

------------

you may make use of library janitor here
```
d1 <- data.frame(a=c(1,2,3))
d2 <- data.frame(a=c(3,4,5))
d3 <- cbind(d1,d2)

janitor::clean_names(d3)
#>   a a_2
#> 1 1   3
#> 2 2   4
#> 3 3   5
```

[reprex package](https://reprex.tidyverse.org)


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-05-22 18:03:48

------------

We can also do `match`
```
d3[match('a', names(d3))][[2]]
```



------------
    
    