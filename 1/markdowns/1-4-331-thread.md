
# Post \#70226329 [Link](https://stackoverflow.com/questions/70226329/)

## Use dplyr's if_else function with functional true/false values

**Vote**: 2 (631/702) **Views**: 1422 (627/702) 

**Internal ID** \#1-4-331

Created at 2021-12-04 14:06:56

Tags: `r` `dplyr`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------


## Question


How can dplyr's `if_else()` function be used such that its output is a function? With `ifelse()` from `base` this is trivial but with `if_else()` something goes wrong. Is that intended behavior or am I just not able to do that properly?

## Reprex


```
x <- 2
ifelse(x == 2, min, max)
#> function (..., na.rm = FALSE)  .Primitive("min")
dplyr::if_else(x == 2, min, max)
#> Error in true[rep(NA_integer_, length(condition))]: object of type 'builtin' is not subsettable
```

[reprex package](https://reprex.tidyverse.org)


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-12-04 17:07:25

------------

You shouldn't use `ifelse` for this. `ifelse` and `if_else` are for vectors--specifically for when . You can't make vectors of functions, so `ifelse` is a poor choice for returning a function. Your code will only work in the special case when the input has length 1---which is what the control/flow function `if()` is for.
Your `ifelse` code will fail with a strange error if `x` has length > 1, try setting `x = 2:3` and running your code.
```
> x = 2:3
> ifelse(x == 2, min, max)
Error in rep(yes, length.out = len) : 
  attempt to replicate an object of type 'builtin'
```

`if()`, on the other hand, will still check the first element and return the correct result for the first element while giving you a warning  about the input length:
```
> if(x == 2) min else max
function (..., na.rm = FALSE)  .Primitive("min")
Warning message:
In if (x == 2) min else max :
  the condition has length > 1 and only the first element will be used
```

When you're checking a condition that should always have length 1 you should use `if(){}else{}`, and the result can be literally any expression, it can be assigned, or it can be arbitrary code that is run. Save `ifelse` (and `if_else`) for vectors as intended.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-12-04 19:43:27

------------

If `x` is always a scalar then using `if` as described in @Gregor Thomas answer is the correct approach.
Another option would be to use `switch`, which makes sense, if you have more than two options and want to minimize the control flow:
```
x <- 2
min_max <- switch(as.character(x), `2` = min, max)
```

If, however, you are actually dealing with a vector for example in a {dplyr} pipeline, then using `character` vectors instead of functions in combination with `do.call` is one approach.
Here is a short example where we want to use a different function (`min` or  `max`) depending on the `am` group in `mtcars`:
```
library(dplyr)

mtcars %>% 
  nest_by(am) %>% 
  mutate(min_max = if_else(am == 1, "min", "max"),
         mpg = summarise(data, x = do.call(min_max, list(mpg)))$x)

#> # A tibble: 2 x 4
#> # Rowwise:  am
#>      am                data min_max   mpg
#>   <dbl> <list<tibble[,10]>> <chr>   <dbl>
#> 1     0           [19 × 10] max      24.4
#> 2     1           [13 × 10] min      15
```

Another approach is to wrap each `min`, `max` in `list()`. In this case, we do not need `do.call`:
```
library(dplyr)

mtcars %>% 
  nest_by(am) %>% 
  mutate(min_max = if_else(am == 1, list(min), list(max)),
         mpg = summarise(data, x = min_max(mpg))$x)

#> # A tibble: 2 x 4
#> # Rowwise:  am
#>      am                data min_max   mpg
#>   <dbl> <list<tibble[,10]>> <list>  <dbl>
#> 1     0           [19 × 10] <fn>     24.4
#> 2     1           [13 × 10] <fn>     15
```

[reprex package](https://reprex.tidyverse.org)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-04 14:21:03

------------

I don't think `if_else` is feasible or preferable over base `ifelse` in this case. `dplyr::if_else` uses subsetting in its source (use `if_else` without brackets). To recreate the error, try running `min[]`.
We can try to "repair" the function by altering the code that intends to coerce the output to be of the same class as the `true` and `false` values by subsetting it:
```
out <- true[rep(NA_integer_, length(condition))]
# to pseudocode
out <- "whatever the typeof(true) value is"
```

and use `:::` to get the appropriate internal functions that `dplyr` uses.
```
if_else <- function (condition, true, false, missing = NULL) {
  if (!is.logical(condition)) {
    bad_args("condition", "must be a logical vector, not {friendly_type_of(condition)}.")
  }
  out <- NA_integer_
  out <- dplyr:::replace_with(out, condition, true, dplyr:::fmt_args(~true),
                      glue::glue("length of {fmt_args(~condition)}"))
  out <- dplyr:::replace_with(out, !condition, false, dplyr:::fmt_args(~false),
                      glue::glue("length of {fmt_args(~condition)}"))
  out <- dplyr:::replace_with(out, is.na(condition), missing, dplyr:::fmt_args(~missing),
                      glue::glue("length of {fmt_args(~condition)}"))
  out
}
x = 5
if_else(x == 2, min, max)

Error: `true` must be an integer vector, not a primitive function.
```

This error is the result of a type check in internal function `check_type` within `replace_with`, that looks like this:
```
if (identical(typeof(x), typeof(template))) {return()}
```

Here `template` is `out`, defined in `if_else`.
- - 
A solution to outputting a function `min` or `max` from `dplyr::if_else` would then be how to coerce `out` to be of type , and to redefine`dplyr` internal functions, for example `replace_with` that uses another another subsetting operation that fails for the same reason as `min[]`.
That is, changing `out` to a builtin like `min` still throws an error:
```
if_else <- function(...){
  ...
  out <- # if out <- min
  ...
}
Error in x[i] <- val : object of type 'builtin' is not subsettable
```

So, this is intended behavior, but perhaps `object of type 'builtin' is not subsettable` is not the intended error message to be displayed.


------------
    
    