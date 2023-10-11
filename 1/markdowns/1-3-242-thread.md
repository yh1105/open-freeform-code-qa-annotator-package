
# Post \#65593796 [Link](https://stackoverflow.com/questions/65593796/)

## Coverting all the list elements to string using Python?

**Vote**: 4 (532/702) **Views**: 5065 (520/702) 

**Internal ID** \#1-3-242

Created at 2021-01-06 10:08:59

Tags: `python` `string` `list`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a list:
```
roll = [1, 2, 3]
```

For converting each of the element into a string, can we directly do `roll = str(roll)` rather that iterating over it and calling `str()` on each element? Is this a correct way?
I am new to Python, any hint in the right direction will do!


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2021-01-06 10:16:23

------------

That wouldn't work, since that would convert the  list into one string:
```
roll = [1, 2, 3]
roll = str(roll)
print(roll)
# Prints [1, 2, 3]
print(type(roll))
# Prints <class 'str'>
```

You can instead use a [list comprehension](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions), to convert each item in the list one by one:
```
roll = [1, 2, 3]
roll = [str(r) for r in roll]
print(roll)
# Prints ['1', '2', '3']
print(type(roll))
# Prints <class 'list'>
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-01-06 10:36:52

------------

Thinking in terms of types, the original variable you have is of type `List[int]` (`[1, 2, 3]`).
When using the `str` function, the result is an element of type `str` (aka `'[1, 2, 3]'`), but that's not the result that you want since you want to convert each individual element in the list to `str`, which makes the desired type to be `List[str]` (`['1', '2', '3']`).
In python, there are several canonical ways to apply a function to each individual element:

### List Comprehension


```
new_roll = [str(x) for x in roll] # ['1', '2', '3']
```

This method is the simplest. It is basically a one-liner that does the followings:
```
new_roll = []
for x in roll:
    new_roll.append(str(x))
```

Notice that the original variable `roll` won't be mutated since it is creating a new list.

### Map


Another way to do the same thing is by using the `map` function in python. However for efficiency reason, the type of the output is of type `Map[str]` instead of type `List[str]`.
```
roll_map = map(str, roll)
for x in roll_map:
    print(x)
```

It is basically doing the followings:
```
roll = [1, 2, 3]
for x in roll:
    str_x = str(x)
    print(str_x)
```

Notice that no new list has been created in order to save memory. This method is generally applicable when you need the result of a function applied to a list (or an iterator in general) but you do not want to store the results of the function application (to save memory).
A common hack to obtain a list via the `map` function is by `list(map(str, roll))`.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-01-06 10:17:49

------------

Yes, this can be done without iterating over it using loop in following way:
```
roll = list(map(str, roll))
```



------------
    
    