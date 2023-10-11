
# Post \#71126989 [Link](https://stackoverflow.com/questions/71126989/)

## How to sort a list alphabetically?

**Vote**: 3 (575/702) **Views**: 1591 (621/702) 

**Internal ID** \#1-4-316

Created at 2022-02-15 13:07:27

Tags: `r` `list` `sorting`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

It is necessary to create a list of random letters of the alphabet. And then sort it alphabetically. To create such a list, I use the following code:
```
# Creating a random vector of letters
random_text_data = sample(letters, 10)
random_text_data

# Convert to list
list_text_data = as.list(random_text_data)
list_text_data
```

In the console I get the following:
```
> random_text_data
 [1] "h" "m" "q" "b" "z" "i" "y" "f" "d" "e"
> # Convert to list
> list_text_data = as.list(random_text_data)
> list_text_data
[[1]]
[1] "h"

[[2]]
[1] "m"

[[3]]
[1] "q"

[[4]]
[1] "b"

[[5]]
[1] "z"

[[6]]
[1] "i"

[[7]]
[1] "y"

[[8]]
[1] "f"

[[9]]
[1] "d"

[[10]]
[1] "e"
```

Now I need to sort it alphabetically. I have tried the following:
```
# Sort list alphabetically
sort_data = sort(list_text_data)
```

But get error:
```
> sort_data = sort(list_text_data)
Error in sort.int(x, na.last = na.last, decreasing = decreasing, ...) : 
'x' must be elementary
```

How should you sort?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-02-15 13:17:04

------------

Here's a method that do not need to `unlist()`. After making the list with random letters, assign names to the list with the content of the list. Then order the names within the list.

## Input


```
random_text_data = sample(letters, 10)

[1] "c" "v" "m" "g" "h" "l" "d" "i" "u" "y"
```


## Original solution


```
list_text_data = as.list(random_text_data)

list_text_data <- setNames(list_text_data, list_text_data)

list_text_data[order(names(list_text_data))]

$c
[1] "c"

$d
[1] "d"

$g
[1] "g"

$h
[1] "h"

$i
[1] "i"

$l
[1] "l"

$m
[1] "m"

$u
[1] "u"

$v
[1] "v"

$y
[1] "y"
```


## Updated solution


Inspired by @zx8754's answer, you don't actually need to change names of the list, you can use `setNames` within `order`, then the output will be an unnamed list.
```
list_text_data[order(names(setNames(list_text_data, list_text_data)))]

[[1]]
[1] "c"

[[2]]
[1] "d"

[[3]]
[1] "g"

[[4]]
[1] "h"

[[5]]
[1] "i"

[[6]]
[1] "l"

[[7]]
[1] "m"

[[8]]
[1] "u"

[[9]]
[1] "v"

[[10]]
[1] "y"
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-02-15 13:22:09

------------

Convert character to integer, then order:
```
# reproducible example data
set.seed(1); random_text_data = sample(letters, 3)
list_text_data = as.list(random_text_data)
# [[1]]
# [1] "y"
# 
# [[2]]
# [1] "d"
# 
# [[3]]
# [1] "g"


# sort
list_text_data[ order(sapply(list_text_data, utf8ToInt)) ]
# [[1]]
# [1] "d"
# 
# [[2]]
# [1] "g"
# 
# [[3]]
# [1] "y"
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-15 13:27:43

------------

We may have to flatten the list first before sorting the elements. We can try the `sort` + `unlist` like below
```
relist(sort(unlist(list_text_data)), list_text_data)
```



------------
    
    