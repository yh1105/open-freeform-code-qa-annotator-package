
# Post \#68244369 [Link](https://stackoverflow.com/questions/68244369/)

## Python: How to str.replace multiple different strings with the same replacement?

**Vote**: 2 (631/702) **Views**: 4812 (527/702) 

**Internal ID** \#1-3-310

Created at 2021-07-04 11:42:01

Tags: `python`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am trying to replace multiple different strings with the same string replace rule.
```
list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']

for i in list_names:
    vari1 = list_names[1]
    vari2 = list_names[2]
    vari3 = list_names[3]
```

I am trying to replace the ! and the ? above with ''.
At the moment I can do this string by string using the below code:
```
list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']
for i in list_names:
    vari1 = list_names[1]
    vari2 = list_names[2]
    vari3 = list_names[3]
    vari1 = vari1.replace('!', '').replace('?', '')
    vari2 = vari2.replace('!', '').replace('?', '')
    vari3 = vari3.replace('!', '').replace('?', '')
```

I am aware it doesn't make sense to use a loop in the above code. But I intend to build on this.
Given that the string replaces are the same for all 3 variables, is there any easy way to do this for all of them at once?
Thanks!


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2021-07-04 11:45:49

------------

You can just use 
```
>>> [x.replace('!', '').replace('?','') for x in list_names]
['sdf', 'sdfds', 'afsafsa', 'afasf']
```

Also, if you have multiple strings to replace, you can even use  combining them by `|` using `re.sub` function as mentioned below:
```
>>> import re
>>> [re.sub('\?|!', '', x) for x in list_names]
['sdf', 'sdfds', 'afsafsa', 'afasf']
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-07-04 11:45:16

------------

You are doing wrong code. You can simply iterate through the indices of the list and convert all of them just like below:
```
list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']
for index in range(len(list_names)):
    list_names[index] = list_name[index].replace("!", '').replace('?', '')
```


#### how does range(len(list_names)) work?


first the length of `list_names` is calculated which in this case is 4. Now using range function we are able to get a list `[0, 1, 2, 3]` which are all the possible indices. By going through the list replacing each index we are able to update values.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-07-04 11:47:04

------------

If the characters to replace are only at the start or end of the string just use `strip('!?')`:
```
for n in list_names:
    print(n.strip('!?'))
```

If you need to replace something anywhere in the string, you need a regular expression as others suggested:
```
import re

for n in list_names:
    print(re.sub('[?!]', '', n))
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-07-04 12:01:26

------------

Consider using [maps](https://docs.python.org/3/library/functions.html?highlight=map%20built#map):
```
list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']
list_names = [*map(lambda s: s.replace(...), list_names)]
```

If you just need a collection, and want to enumerate it once, then more efficient not to convert it to list, so it would be:
```
list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']
list_names_map = map(lambda s: s.replace(...), list_names)
```

Actual execution of `map` will be postponed till place where you put it into `for` clause.
It might be also helpfull if you want the result to be changed whenever you change source collection. Special case is DB query of big amount of records, which might be done within series embedded queries.
As it was noticed by [@ThePyGuy](https://stackoverflow.com/users/9136348/thepyguy), another step to improve performance is to use single pass replacement for two symbols, depending on interpreter type, it might [re.sub](https://docs.python.org/3/library/re.html?highlight=re%20sub#re.sub) or a direct implementation:
```
import re
list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']
list_names_map = map(lambda s: re.sub(r'[\?\!]', '', s), list_names)

# Do you still need a list?
list_names = list(list_names_map)
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-07-04 12:10:36

------------

This also works if you want in single line without list comprehension
```
import ast

list_names = ['!sdf?', '!sdfds?', '!afsafsa?', '!afasf?']

modified_list_names = ast.literal_eval(str(list_names).replace("!",".").replace("?","."))

print(modified_list_names)
```



------------
    
    