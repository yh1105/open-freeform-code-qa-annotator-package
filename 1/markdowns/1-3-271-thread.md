
# Post \#67574893 [Link](https://stackoverflow.com/questions/67574893/)

## Python split string by multiple delimiters following a hierarchy

**Vote**: 23 (239/702) **Views**: 4713 (530/702) 

**Internal ID** \#1-3-271

Created at 2021-05-17 18:04:30

Tags: `python` `regex` `string`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python` `regex`)

----------

**Notepad**


----------

I want to split strings only once based on multiple delimiters like "and", "&" and "-" in that order. Example:
```
'121 34 adsfd' -> ['121 34 adsfd']
'dsfsd and adfd' -> ['dsfsd ', ' adfd']
'dsfsd & adfd' -> ['dsfsd ', ' adfd']
'dsfsd - adfd' -> ['dsfsd ', ' adfd']
'dsfsd and adfd and adsfa' -> ['dsfsd ', ' adfd and adsfa']
'dsfsd and adfd - adsfa' -> ['dsfsd ', ' adfd - adsfa']
'dsfsd - adfd and adsfa' -> ['dsfsd - adfd ', ' adsfa']
```

I tried the below code to achieve this:
```
import re
re.split('and|&|-', string, maxsplit=1)
```

It works for all cases except the last one. As it does not follow the hierarchy, for the last one it returns:
```
'dsfsd - adfd and adsfa' -> ['dsfsd ', ' adfd and adsfa']
```

How can I achieve this?


----------
        
## Answer \#0

**Accepted** Vote: 25

Created at 2021-05-17 18:16:19

------------

Try:
```
import re

tests = [
    ["121 34 adsfd", ["121 34 adsfd"]],
    ["dsfsd and adfd", ["dsfsd ", " adfd"]],
    ["dsfsd & adfd", ["dsfsd ", " adfd"]],
    ["dsfsd - adfd", ["dsfsd ", " adfd"]],
    ["dsfsd and adfd and adsfa", ["dsfsd ", " adfd and adsfa"]],
    ["dsfsd and adfd - adsfa", ["dsfsd ", " adfd - adsfa"]],
    ["dsfsd - adfd and adsfa", ["dsfsd - adfd ", " adsfa"]],
]

for s, result in tests:
    res = re.split(r"and|&(?!.*and)|-(?!.*and|.*&)", s, maxsplit=1)
    print(res)
    assert res == result
```

Prints:
```
['121 34 adsfd']
['dsfsd ', ' adfd']
['dsfsd ', ' adfd']
['dsfsd ', ' adfd']
['dsfsd ', ' adfd and adsfa']
['dsfsd ', ' adfd - adsfa']
['dsfsd - adfd ', ' adsfa']
```


---


Explanation:
The regex `and|&(?!.*and)|-(?!.*and|.*&)` uses 3 alternatives.

1. We match and always or:
2. We match & only if there isn't and ahead (using the negative look-ahead (?! ) or:
3. We match - only if there isn't and or & ahead.


We're using this pattern in `re.sub` -> splitting only on the first match.


------------
    
    
## Answer \#1

 Vote: 38

Created at 2021-05-17 18:14:52

------------

This would be impractical with a single regular expression. You could get it to work with negative lookbehinds, but it would get quite complicated with each additional delimiter. It's pretty trivial to do this with plain old `str.split()` and multiple lines. All you have to do is check if splitting with the current delimiter gives you two elements. If it does, that's your answer. If not, move on to the next delimiter:
```
def split_new(inp, delims):
    for d in delims:
        result = inp.split(d, maxsplit=1)
        if len(result) == 2: return result

    return [inp] # If nothing worked, return the input
```

To test this:
```
teststrs = ['121 34 adsfd' , 'dsfsd and adfd', 'dsfsd & adfd' , 'dsfsd - adfd' , 'dsfsd and adfd and adsfa' , 'dsfsd and adfd - adsfa' , 'dsfsd - adfd and adsfa' ]
for t in teststrs:
    print(repr(t), '->', split_new(t, ['and', '&', '-']))
```

outputs
```
'121 34 adsfd' -> ['121 34 adsfd']
'dsfsd and adfd' -> ['dsfsd ', ' adfd']
'dsfsd & adfd' -> ['dsfsd ', ' adfd']
'dsfsd - adfd' -> ['dsfsd ', ' adfd']
'dsfsd and adfd and adsfa' -> ['dsfsd ', ' adfd and adsfa']
'dsfsd and adfd - adsfa' -> ['dsfsd ', ' adfd - adsfa']
'dsfsd - adfd and adsfa' -> ['dsfsd - adfd ', ' adsfa']
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-05-17 18:19:36

------------

You can keep a list of the delimiters, ordered by their value. Then, you can combine `re.split` with `re.findall` to only use delimiters produced from the latter that are the least valuable in the split, per the ranking in `ops`:
```
import re
def split_order(s):
   r, ops = re.findall('(?<=\s)and(?=\s)|\&|\-', s), ['and', '&', '-']
   m = -1 if not r else min([ops.index(i) for i in r])
   a, *b = re.split('|'.join(l:=[i for i in r if ops.index(i) == m]), s)
   return [s] if not l else ([a] if not b else [a, s[len(a)+len(l[0]):]])


vals = ['121 34 adsfd' , 'dsfsd and adfd', 'dsfsd & adfd' , 'dsfsd - adfd' , 'dsfsd and adfd and adsfa' , 'dsfsd and adfd - adsfa' , 'dsfsd - adfd and adsfa' ]
for i in vals:
   print(split_order(i))
```

Output:
```
['121 34 adsfd']
['dsfsd ', ' adfd']
['dsfsd ', ' adfd']
['dsfsd ', ' adfd']
['dsfsd ', ' adfd and adsfa']
['dsfsd ', ' adfd - adsfa']
['dsfsd - adfd ', ' adsfa']
```



------------
    
    