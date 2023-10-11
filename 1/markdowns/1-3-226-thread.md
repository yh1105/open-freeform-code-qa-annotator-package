
# Post \#73057180 [Link](https://stackoverflow.com/questions/73057180/)

## Split a string if character is present else don't split

**Vote**: 2 (631/702) **Views**: 560 (691/702) 

**Internal ID** \#1-3-226

Created at 2022-07-20 19:27:29

Tags: `python` `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a `string` like below in `python`
```
testing_abc
```

I want to split string based on `_` and extract the `2` element
I have done like below
```
split_string = string.split('_')[1]
```

I am getting the correct output as expected
```
abc
```

Now I want this to work for below strings
```
1) xyz
```

When I use
```
split_string = string.split('_')[1]
```

I get below error
```
list index out of range
```

expected output I want is `xyz`
```
2) testing_abc_bbc
```

When I use
```
split_string = string.split('_')[1]
```

I get `abc` as output
expected output I want is `abc_bbc`
Basically What I want is
```
1) If string contains `_` then print everything after the first `_` as variable
2) If string doesn't contain `_` then print the string as variable
```

How can I achieve what I want


----------
        
## Answer \#0

**Accepted** Vote: 9

Created at 2022-07-20 19:35:29

------------

Set the `maxsplit` argument of `split` to `1` and then take the last element of the resulting list.
```
>>> "testing_abc".split("_", 1)[-1]
'abc'
>>> "xyz".split("_", 1)[-1]
'xyz'
>>> "testing_abc_bbc".split("_", 1)[-1]
'abc_bbc'
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-07-20 19:34:12

------------

You can use list slicing and `str.join` in case `_` is in the string, and you can just get the first element of the split (which is the only element) in the other case:
```
sp = string.split('_')
result = '_'.join(sp[1:]) if len(sp) > 1 else sp[0]
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-07-20 19:55:12

------------

All of the ways are good but there is a very simple and optimum way for this.
Try:
```
s = 'aaabbnkkjbg_gghjkk_ttty'
try:
    ans = s[s.index('_')+1:]
except:
    ans = s
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-07-20 19:38:17

------------

Ok so your error is supposed to happen/expected because you are using `'_'` as your delimiter and it doesn't contain it.
See [How to check a string for specific characters?](https://stackoverflow.com/questions/5188792/how-to-check-a-string-for-specific-characters) for character checking.
If you want to only split iff the string contains a `'_'` and only on the first  one,
```
input_string = "blah_end"
delimiter = '_'

if delimiter in input_string:
     result = input_string.split("_", 1)[1] # The ",1" says only split once
else:
     # Do whatever here. If you want  a space, " " to be a delimiter too you can try that.
     result = input_string
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-07-20 19:43:37

------------

this code will solve your problem
```
txt = "apple_banana_cherry_orange"
# setting the maxsplit parameter to 1, will return a list with 2 elements!
x = txt.split("_", 1)
print(x[-1])
```



------------
    
    