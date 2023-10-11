
# Post \#65648289 [Link](https://stackoverflow.com/questions/65648289/)

## How to find min and max values in a 3d array in numpy, and group the results?

**Vote**: 2 (631/702) **Views**: 3503 (556/702) 

**Internal ID** \#1-3-181

Created at 2021-01-09 22:32:50

Tags: `python` `arrays` `numpy` `numpy-ndarray`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a 3D NumPy array like so:
```
[[[4 1 5 2 5 5 7 8 9 7]
  [7 4 2 4 7 8 4 1 3 5]
  [6 1 2 1 1 1 2 3 7 6]
  [5 5 5 0 5 4 3 8 7 1]
  [2 8 6 7 4 7 5 5 5 1]]

 [[9 9 5 8 0 7 3 9 8 1]
  [9 1 9 5 7 4 5 4 7 0]
  [1 0 4 8 7 3 4 3 8 8]
  [8 1 3 1 7 0 9 9 3 8]
  [4 0 2 3 8 2 0 1 2 4]]

 [[1 6 2 4 4 0 2 3 0 3]
  [9 6 8 6 6 5 6 9 4 1]
  [0 4 0 2 9 1 1 2 4 6]
  [6 1 9 9 7 8 9 7 6 8]
  [9 3 9 0 7 0 0 0 7 0]]]
```

With it, I like to create a 2d ndarray like so:
```
[[6]
 [9]
 [9]]
```

Where each element of this 2d array is the max value of the third column on the original array:
[](https://i.stack.imgur.com/GyEtA.png)
I have been a couple of hours trying to puzzle this out but no luck...
I'm asking for a 2d array as the output because I have other calculations to make (say, I also need to min value of the second column in a similar fashion), but I think I can extrapolate those from this.
Any pointers are greatly appreciated!


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-01-09 22:41:27

------------

Have you tried this:
```
import numpy as np
x = np.random.randint(0, 10, (3, 5, 10))
print(x)
maxes = x[:,:,2].max(axis=1)
print(maxes)

[[[5 0 6 6 4 7 5 0 4 8]
  [0 6 8 8 2 1 7 5 4 3]
  [2 7 5 5 0 2 6 8 6 3]
  [5 9 7 5 1 1 5 4 8 7]
  [0 2 3 7 8 1 9 1 2 6]]

 [[8 9 4 3 3 6 0 4 9 1]
  [1 5 6 4 3 2 7 7 0 2]
  [3 2 0 1 9 6 5 8 0 5]
  [6 1 5 9 1 6 4 7 4 5]
  [7 2 5 8 6 8 5 1 9 5]]

 [[9 4 0 9 0 6 3 7 4 1]
  [4 1 4 9 1 1 1 2 0 6]
  [7 3 3 2 5 2 0 6 9 1]
  [1 7 0 1 8 1 3 8 6 4]
  [6 9 0 2 6 0 2 1 7 7]]]
[8 6 4]
```

To understand how this works checkout:
- [Numpy array indexing](https://numpy.org/doc/stable/reference/arrays.indexing.html)- [np.ndarray.max](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.max.html)
And, to get the maximum of all the columns:
```
col_maximums = x.max(axis=1)
print(col_maximums)
                                                                                    
[[5 9 8 8 8 7 9 8 8 8]
 [8 9 6 9 9 8 7 8 9 5]
 [9 9 4 9 8 6 3 8 9 7]]
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-01-09 22:49:49

------------

What you need to do is take the columns that you want(in this case it is `arr[:,:,2]`). This will have the shape of `(3,5)`. `arr=np.max(arr[:,:,2],axis=-1)` then yields the array contents that you want with a shape of `(3)`. You can then call `arr=np.reshape(arr,(3,1))` to get the shape you wanted.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-01-09 23:04:57

------------

Below I tried to do the operation step by step and printed the shape after every operation.
the `(2,)` is important in order to preserve the dimension and stay with 2d like you wanted (otherwise it had auto-collapse the singleton dimension).
```
import numpy as np

a = np.array([
    [[4, 1, 5, 2, 5, 5, 7, 8, 9, 7],
     [7, 4, 2, 4, 7, 8, 4, 1, 3, 5],
     [6, 1, 2, 1, 1, 1, 2, 3, 7, 6],
     [5, 5, 5, 0, 5, 4, 3, 8, 7, 1],
     [2, 8, 6, 7, 4, 7, 5, 5, 5, 1]],

    [[9, 9, 5, 8, 0, 7, 3, 9, 8, 1],
     [9, 1, 9, 5, 7, 4, 5, 4, 7, 0],
     [1, 0, 4, 8, 7, 3, 4, 3, 8, 8],
     [8, 1, 3, 1, 7, 0, 9, 9, 3, 8],
     [4, 0, 2, 3, 8, 2, 0, 1, 2, 4]],

    [[1, 6, 2, 4, 4, 0, 2, 3, 0, 3],
     [9, 6, 8, 6, 6, 5, 6, 9, 4, 1],
     [0, 4, 0, 2, 9, 1, 1, 2, 4, 6],
     [6, 1, 9, 9, 7, 8, 9, 7, 6, 8],
     [9, 3, 9, 0, 7, 0, 0, 0, 7, 0]]
])

print(a.shape)  # --> (3, 5, 10)
col2 = a[:, :, (2,)]
print(col2.shape)  # --> (3, 5, 1)
max_col2 = col2.max(axis=1)
print(max_col2.shape)  # --> (3, 1)
print(max_col2)  # -->

[[6]
 [9]
 [9]]
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-01-10 04:33:57

------------

You alternatively could use the `keepdims` argument of `np.max` like this (`a` is your original array):
```
a[:,:,2].max(1, keepdims=True)
```

Output:
```
[[6]
 [9]
 [9]]
```



------------
    
    