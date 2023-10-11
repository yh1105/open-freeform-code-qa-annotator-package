
# Post \#72320130 [Link](https://stackoverflow.com/questions/72320130/)

## Filter a list containing specific string

**Vote**: 2 (631/702) **Views**: 905 (665/702) 

**Internal ID** \#1-3-224

Created at 2022-05-20 13:55:51

Tags: `python` `list`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------


I have a specific set of a long list containing many asset pairs with different endings: `BTCUSDT` `ETHBTC` `ANKRETH`... From this list, I would like to filter out the symbols ending with USDT.

I have tried using `filter` and `all` with iterations, however, none provides the exact result.
Here are my attempts:

```
my_set = ['LUNAUSDT', 'ETHBTC', 'ETHBNB', 'BTCUSDT', 'MANATUSD', 'ALICEETH' ]
my_set = [word for word in symbols if 'USDT' in my_set]
my_set
```

> This results in an empty set.

```
keyword = ['USDT']
my_set = ['LUNAUSDT', 'ETHBTC', 'ETHBNB', 'BTCUSDT', 'MANATUSD', 'ALICEETH' ]
final = [ x for x in my_set in all(keyword in keyword for keyword in my_set)]
final
```

This results in the error:
> TypeError: argument of type 'bool' is not iterable

```
my_final_set = filter(lambda x:x.endswith(("USDT")), my_set)
my_final_set
```

> This shows: <filter at 0x7fe537eebf10>
I basically want my final list with all symbols ending with `USDT`

```
my_set = ['LUNAUSDT', 'ETHBTC', 'ETHBNB', 'BTCUSDT', 'MANATUSD', 'ALICEETH' ]
```

results in: `['LUNAUSDT', 'BTCUSDT']`
Any help or advice on what I'm doing wrong would be massively appreciated. Thanks in advance!


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-05-20 14:01:26

------------

You're almost there. Try this:
```
my_set = ['LUNAUSDT', 'ETHBTC', 'ETHBNB', 'BTCUSDT', 'MANATUSD', 'ALICEETH' ]
final = [word for word in my_set if 'USDT' in word]
print(final)
```

For your , make this change:
```
my_final_set = list(filter(lambda x:x.endswith(("USDT")), my_set))
```

One another method is to use `.endswith()`:
```
final = [word for word in my_set if word.endswith('USDT')]
print(final)
```

Output:
```
['LUNAUSDT', 'BTCUSDT']
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-05-20 14:27:11

------------


#### Attempt 1


> ```
[word for word in symbols if 'USDT' in my_set]
```

This has two mistakes:
- `'USDT' in my_set` checks if `'USDT'` is contained in the input list, not if it is contained in  from the input list. You should have used `'USDT' in word`.- `'USDT' in word` would check if `'USDT'` is contained  in `word` (not just at the end). In order to check if a string  a particular suffix, use `word.endswith('USDT')`.

#### Attempt 2


> ```
[ x for x in my_set in all(keyword in keyword for keyword in my_set)]
```

This makes the least sense of your attempts. `all(...)` returns either `True` or `False`, depending on whether a condition is true for all elements from an iterable. In this case `keyword in keyword` is obviously true for all words `keyword` from `my_set`, so this would be equivalent to
```
[x for x in my_set in True]
```

Here, Python would try to evaluate `my_set in True` as if `True` were some sort of collection. It attempts this by trying to iterate over `True` (and then checking in turn if any item is equal to `my_set`), which is not possible.

#### Attempt 3


> ```
filter(lambda x:x.endswith(("USDT")), my_set)
```

This is mostly correct, however `filter` returns an , which only returns the results as you iterate over it. In order to get a list, you have to consume the iterator:
```
list(filter(lambda x: x.endswith(("USDT")), my_set))
```

which is approximately equivalent to
```
result = []
for y in filter(lambda x: x.endswith(("USDT")), my_set):
    result.append(y)
```

See [Why does foo = filter(...) return a <filter object>, not a list?](https://stackoverflow.com/questions/33174276/why-does-foo-filter-return-a-filter-object-not-a-list)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-20 14:03:16

------------

solution using `filter` function and `list-comprehension`
```
my_set = ['LUNAUSDT', 'ETHBTC', 'ETHBNB', 'BTCUSDT', 'MANATUSD', 'ALICEETH' ]
keyword = 'USDT'

result1 = list(filter(lambda word: word.endswith(keyword), my_set))
result2 = [ word for word in my_set if word.endswith(keyword)]
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-05-20 14:03:42

------------

You can use regex:
```
import re

my_set = ['LUNAUSDT', 'ETHBTC', 'ETHBNB', 'BTCUSDT', 'MANATUSD', 'ALICEETH' ]

my_filtered_set = [ i for i in my_set if re.search('USD$',i) ]
```



------------
    
    