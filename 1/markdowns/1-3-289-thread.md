
# Post \#71889289 [Link](https://stackoverflow.com/questions/71889289/)

## Iterating through list of lists of lists

**Vote**: 3 (575/702) **Views**: 1187 (647/702) 

**Internal ID** \#1-3-289

Created at 2022-04-15 21:54:31

Tags: `python` `list` `loops` `for-loop` `iteration`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am trying to iterate through a 3-D list in python(not numpy but I am willing to convert to a numpy array if this makes it easier) in such a way that from a list like this:
```
a = [[[0, 0], [3, 0]], [[1, 0], [4, 0]], [[2, 0], [6, 0]] ]
```

I can get the output
```
[0,0]
[1,0]
[2,0]
[3,0]
[4,0]
[6,0]
```

I can't figure out how to make it iterate like this...
My code:
```
a = [[[0, 0], [0, 0]], [[1, 0], [0, 0]], [[2, 0], [0, 0]] ]
for i in range(len(a)):
    for z in range(len(a[i])):
        print(a[i][z])
```

I've tried different things but can't seem to get this output.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-04-15 22:06:22

------------

I think you want to print the nth sub-sublists consecutively from each sublist. You could unpack and zip `a` to get an iterable of tuples, then print each pair in them:
```
for tpl in zip(*a):
    for pair in tpl:
        print(pair)
```

Output:
```
[0, 0]
[1, 0]
[2, 0]
[3, 0]
[4, 0]
[6, 0]
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-04-15 22:06:29

------------

Try:
```
print(*[i for v in zip(*a) for i in v], sep="\n")
```

Prints:
```
[0, 0]
[1, 0]
[2, 0]
[3, 0]
[4, 0]
[6, 0]
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-04-15 22:04:45

------------

In case you want to just traverse the elements column wise, you can convert the list to numpy array, then stack the columns and print:
```
import numpy as np
a = [[[0, 0], [3, 0]], [[1, 0], [4, 0]], [[2, 0], [6, 0]] ]
arr = np.stack(np.array(a), axis=1)

for p in arr:
    for x in p:
        print(x)
```

The solution below is based on the previous assumption of printing each item in the sublist based on sorted order:
You can use itertools to flatten the 3d to 2d list and then sort the 2d list based on first element in each sublist. Then print the sorted list.
```
import itertools
a = [[[0, 0], [3, 0]],[[1, 0], [4, 0]],[[2, 0], [6, 0]]]
flat_list = list(itertools.chain(*a))
sorted_list = sorted(flat_list, key=lambda x: x[0])

for i in sorted_list:
    print(i)
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-04-15 21:58:56

------------

This is the correct way of iterating like you're trying to do:
```
a = [[[0, 0], [0, 0]], [[1, 0], [0, 0]], [[2, 0], [0, 0]] ]
for i in range(len(a)):
    for z in range(len(a[i])):
        print((a[i])[z])
```

If it is simpler for you, instead of iterating on the length of the lists with an index, try iterating on the elements of the lists:
```
a = [[[0, 0], [0, 0]], [[1, 0], [0, 0]], [[2, 0], [0, 0]] ]
for a1 in a:
    for a2 in a1:
        print(a2)
```



------------
    
    