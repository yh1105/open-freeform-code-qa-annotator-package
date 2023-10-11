
# Post \#71624289 [Link](https://stackoverflow.com/questions/71624289/)

## Replace elements in lists based on indexes python

**Vote**: -1 (699/702) **Views**: 1266 (640/702) 

**Internal ID** \#1-3-187

Created at 2022-03-26 00:02:23

Tags: `python` `list` `indexing` `replace` `match`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Community of stackoverflow:
I have a list "rest" which is the following:
```
rest=[5, 7, 11, 4]
```

I have another list which is b:
```
b=[21, 22, 33, 31, 23, 15, 19, 13, 6]
```

And I have a "last" list:
```
last=[33, 19, 40, 21, 31, 22, 6, 15, 13, 23]
```

I have to replace the first 4 elements in b with the elements in rest. How can I replace the elements in last according to the matches with b to get the rest elements?
for example:
```
5   7   11   4   #elements from rest
b= [21, 22, 33, 31, 23, 15, 19, 13, 6]
```

to get last list as the following:
```
last=[11, 19, 40, 5, 4, 7, 6, 15, 13, 23]  #elements that matched with b were replaced by rest
```

How can I do this?


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-03-26 00:12:20

------------

Try this:
```
rest=[5, 7, 11, 4]
b=[21, 22, 33, 31, 23, 15, 19, 13, 6]
last=[33, 19, 40, 21, 31, 22, 6, 15, 13, 23]

for i, l in enumerate(last):
    if l in b:
        if b.index(l) < len(rest):
            last[i] = rest[b.index(l)]
            
print(last)
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-26 00:12:22

------------

You can try to do something like this...
```
rest_change_index = 0
for i in range(len(b)):
   if b[i] in last:
       last_change_index = last.index(b[i])
       last[last_change_index] = rest[rest_change_index]
       rest_change_index += 1
print(last)
```

This iterates through the elements of b, and if an element in last matches the element of b being iterated through in the loop, then it changes that value with the corresponding element of rest (first element of rest for first matching instance, etc.). Let me know if this makes sense.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-26 00:14:33

------------

You can do this as follows:
```
# Get first 4 items in b
b4 = b[:4]
# Create mapping from b to rest
b_to_rest = dict(zip(b4, rest))
# Use dictionary .get to either replace if in rest or keep if not
last = [b_to_rest.get(x,x) for x in last]
```

Firstly, I've defined `b4` as the first 4 items in `b`. Then I've used `zip` and `dict` to map `b` values to `rest` values. Finally I use a list comprehension to replace the items in `last`. `.get(x,x)` means try to get `x` from the dictionary, if it doesn't exist just use `x`.


------------
    
    