
# Post \#72509813 [Link](https://stackoverflow.com/questions/72509813/)

## Replace values in one data.table from another data.table

**Vote**: 1 (672/702) **Views**: 565 (690/702) 

**Internal ID** \#1-4-317

Created at 2022-06-05 17:52:45

Tags: `r` `data.table`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `r`)

----------

**Notepad**


----------

I have a `data.table` called `big` in which I want to replace all corresponding values from `data.table` called `new_big`.
```
library(data.table)
big <- structure(list(id = c("B", "C", "D", "E", "F", "G", "H", "I", 
"J", "K"), col = c(103L, 103L, 102L, 105L, 104L, 103L, 104L, 104L, 
104L, 103L)), row.names = c(NA, -10L), class = c("data.table", 
"data.frame"))
new_big <- structure(list(id = c("B", "E", "G"), col = c(1, 11, 111)), row.names = c(NA, 
-3L), class = c("data.table", "data.frame"))
```

They create:
```
> big
    id col
 1:  B 103
 2:  C 103
 3:  D 102
 4:  E 105
 5:  F 104
 6:  G 103
 7:  H 104
 8:  I 104
 9:  J 104
10:  K 103

> new_big
   id col
1:  B   1
2:  E  11
3:  G 111
```

Here is the desired output -
```
id  col
 1:  B 1
 2:  C 103
 3:  D 102
 4:  E 11
 5:  F 104
 6:  G 111
 7:  H 104
 8:  I 104
 9:  J 104
10:  K 103
```

Is there a way to join these two tables to get the desired output?
I tried following but I could not get the desired output as shown above. Any pointers will be helpful.
```
big[new_big, on = .(id)]
   id col i.col
1:  B 105     1
2:  E 103    11
3:  G 101   111
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-06-05 18:00:38

------------

We do the join first and `fcoalesce` with the 'col' object - also make sure that the types are same or else it return error
```
big[new_big, on = .(id), col2 := i.col][, .(id, 
     col = fcoalesce(col2, as.numeric(col)))]
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-06-05 19:14:20

------------

If `id` is unique in `big`, try this:
```
rbind(new_big, big[!new_big, on="id"])
```

If not, do this:
```
new_big[big, on="id"][is.na(col), col:=i.col][, i.col:=NULL]
```

Output:
```
id col
 1:  B   1
 2:  E  11
 3:  G 111
 4:  C 103
 5:  D 102
 6:  F 104
 7:  H 104
 8:  I 104
 9:  J 104
10:  K 103
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-06-05 18:15:50

------------

Not as elegant as @akrun - I did:
```
vec <- new_big$col
names(vec) <- new_big$id

v <- big$col
names(v) <- big$id

res <- vec[names(v)]

res[is.na(res)] <- v[is.na(res)]
names(res)[is.na(names(res))] <- names(v)[is.na(names(res))]

res_df <- data.frame(id = names(res), col = res)
```



------------
    
    