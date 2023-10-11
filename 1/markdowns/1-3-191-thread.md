
# Post \#64127075 [Link](https://stackoverflow.com/questions/64127075/)

## How to retrieve partial matches from a list of strings

**Vote**: 29 (199/702) **Views**: 32461 (223/702) 

**Internal ID** \#1-3-191

Created at 2020-09-29 20:21:14

Tags: `python` `string` `list` `filter`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

For approaches to retrieving partial matches in a  list, go to:
- [How to return a subset of a list that matches a condition?](https://stackoverflow.com/questions/35101426/how-to-return-a-subset-of-a-list-that-matches-a-condition)- [Python: Find in list](https://stackoverflow.com/questions/9542738/python-find-in-list)

---


But if you're looking for how to retrieve partial matches for a list of , you'll find the best approaches concisely explained in the answer below.
[SO: Python list lookup with partial match](https://stackoverflow.com/questions/6117318/python-list-lookup-with-partial-match) shows how to return a `bool`, if a `list` contains an element that partially matches (e.g. `begins`, `ends`, or `contains`) a certain string. But how can you , instead of `True` or `False`

### Example:


```
l = ['ones', 'twos', 'threes']
wanted = 'three'
```

Here, the approach in the linked question will return `True` using:
```
any(s.startswith(wanted) for s in l)
```

So how can you return the element `'threes'` instead?


----------
        
## Answer \#0

**Accepted** Vote: 42

Created at 2020-09-29 20:25:30

------------

- [startswith](https://docs.python.org/3/library/stdtypes.html#str.startswith)[in](https://docs.python.org/3/reference/expressions.html#in)- `in`- `list-comprehension``filter`- `list-comprehension``in`- - `l = list(map(str.lower, l))`- 

## filter:


- [filter](https://docs.python.org/3/library/functions.html#filter)`filter``list()``list`
```
l = ['ones', 'twos', 'threes']
wanted = 'three'

# using startswith
result = list(filter(lambda x: x.startswith(wanted), l))

# using in
result = list(filter(lambda x: wanted in x, l))

print(result)
[out]:
['threes']
```


## list-comprehension


```
l = ['ones', 'twos', 'threes']
wanted = 'three'

# using startswith
result = [v for v in l if v.startswith(wanted)]

# using in
result = [v for v in l if wanted in v]

print(result)
[out]:
['threes']
```


## Which implementation is faster?


- `words``nltk v3.7`- `'three'`- `['three', 'threefold', 'threefolded', 'threefoldedness', 'threefoldly', 'threefoldness', 'threeling', 'threeness', 'threepence', 'threepenny', 'threepennyworth', 'threescore', 'threesome']`
```
from nltk.corpus import words

%timeit list(filter(lambda x: x.startswith(wanted), words.words()))
%timeit list(filter(lambda x: wanted in x, words.words()))
%timeit [v for v in words.words() if v.startswith(wanted)]
%timeit [v for v in words.words() if wanted in v]
```


### %timeit results


```
62.8 ms ± 816 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
53.8 ms ± 982 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
56.9 ms ± 1.33 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
47.5 ms ± 1.04 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```



------------
    
    
## Answer \#1

 Vote: 8

Created at 2020-09-29 20:25:36

------------

Instead of returning the result of the `any()` function, you can use a for-loop to look for the string instead:
```
def find_match(string_list, wanted):
    for string in string_list:
        if string.startswith(wanted):
            return string
    return None

>>> find_match(['ones', 'twos', 'threes'], "three")
'threes'
```



------------
    
    
## Answer \#2

 Vote: 8

Created at 2020-09-29 20:27:28

------------

A simple, direct answer:
```
test_list = ['one', 'two','threefour']
r = [s for s in test_list if s.startswith('three')]
print(r[0] if r else 'nomatch')
```

Result:
```
threefour
```

Not sure what you want to do in the non-matching case.  `r[0]` is exactly what you asked for if there is a match, but it's undefined if there is no match.  The `print` deals with this, but you may want to do so differently.


------------
    
    
## Answer \#3

 Vote: 6

Created at 2020-09-29 20:35:46

------------

I'd say the most closely related solution would be to use `next` instead of `any`:
```
>>> next((s for s in l if s.startswith(wanted)), 'mydefault')
'threes'
>>> next((s for s in l if s.startswith('blarg')), 'mydefault')
'mydefault'
```

Just like `any`, it stops the search as soon as it found a match, and only takes O(1) space. Unlike the list comprehension solutions, which always process the whole list and take O(n) space.
Ooh, alternatively just use `any` as is but remember the last checked element:
```
>>> if any((match := s).startswith(wanted) for s in l):
        print(match)

threes
>>> if any((match := s).startswith('blarg') for s in l):
        print(match)

>>>
```

Another variation, only assign the matching element:
```
>>> if any(s.startswith(wanted) and (match := s) for s in l):
        print(match)

threes
```

(Might want to include something like `or True` if a matching `s` could be the empty string.)


------------
    
    
## Answer \#4

 Vote: 5

Created at 2020-09-29 20:25:38

------------

this seems simple to me so i might have misread but you could just run it through a foor loop w/ an if statement;
```
l = ['ones', 'twos', 'threes']
wanted = 'three'

def run():
    for s in l:
        if (s.startswith(wanted)):
            return s

print(run())
```

output:
`threes`


------------
    
    