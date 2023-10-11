
# Post \#72618859 [Link](https://stackoverflow.com/questions/72618859/)

## Run two nested for loops in parallel to create matrix

**Vote**: 2 (631/702) **Views**: 777 (678/702) 

**Internal ID** \#1-3-283

Created at 2022-06-14 14:36:17

Tags: `python` `multithreading` `numpy`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I've written a method that takes in an integer "n" and creates a square matrix where the values of each element are dictated by their respective i,j indices.
When I build a small matrix 30x30 it works just fine, but when I try to do something larger like 1000x1000 it takes very long. Is there any way that I can speed it up with multiprocessing?
```
def createMatrix(n):
    matrix = []
    for j in range(1,n+1):
        row = []
        for i in range(1,n+1):
            value = 1/(i+j-1)
            row.append(value)
        matrix.append(row)
    return np.array(matrix)
```



----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2022-06-14 14:52:10

------------

Parallelizing two computation-bound `for` loops in Python is not trivial because of GIL. The good news is that your case is perfectly vectorizeable:
```
def createMatrix(n):
    return 1 / (np.arange(n)[None, :] + np.arange(n)[:, None] + 1)
```

Explanation:
- `X[row][column] = 1/(row+column-1)`- `np.arange(n)`- `[None, :]``[:, None]``1 x n``n x 1`- `numpy``n x n`- `+1``-1`
As a rule of thumb, it is almost never a good idea to use for loops on numpy arrays. A vectorized approach (i.e. matrix form computations) is orders of magnitude faster.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2022-06-14 15:01:06

------------

It's not a good idea to use fors to fill a list then convert it to a matrix. the operation that you have can be vectorized with numpy from scratch. if you think that given the `i,j`, `M(i,j) = 1/(j+i-1)` considering that both indices starts at 1.
Here's my proposal :
```
def createMatrix2(n):
    arr =np.arange(1,n+1)
    xx,yy = np.meshgrid(arr,arr)
    matrix = 1/(xx+yy-1)
    return matrix
```

looking at Marat answer, I think his/her it's better, so tested the 3 methods:
: added wwii method as createMatrix4 (correcting the errors):
```
import numpy as np
from time import time

def createMatrix1(n):
    matrix = []
    for j in range(1,n+1):
        row = []
        for i in range(1,n+1):
            value = 1/(i+j-1)
            row.append(value)
        matrix.append(row)
    return np.array(matrix)

def createMatrix2(n):
    arr =np.arange(1,n+1)
    xx,yy = np.meshgrid(arr,arr)
    matrix = 1/(xx+yy-1)
    return matrix

def createMatrix3(n):
    """Marat's proposed matrix"""
    return 1 / (1 + np.arange(n)[None, :] + np.arange(n)[:, None])

def createMatrix4(n):
    """ wwii method"""
    i,j = np.ogrid[1:n,1:n]
    return 1/(i+j-1)
#test all the three methods
n = 10000

t1 = time()
m1 = createMatrix1(n)
t2 = time()
m2 = createMatrix2(n)
t3 = time()
m3 = createMatrix3(n)
t4 = time()
m4 = createMatrix4(n)
t5 = time()
print(np.allclose(m1,m2))
print(np.allclose(m1,m3))
print(np.allclose(m1,m4))
print("Matrix 1 (OP): ",t2-t1)
print("Matrix 2: (mine)",t3-t2)
print("Matrix 3: (Marat)",t4-t3)
print("Matrix 4: (wwii)",t5-t4)
# the output is:
#True
#True
#True
#Matrix 1 (OP):  18.4886577129364
#Matrix 2: (mine) 1.005324363708496
#Matrix 3: (Marat) 0.43033909797668457
#Matrix 4: (wwii) 0.5138359069824219
```

So Marat's solution is faster. As general comments:
- - 
For last, given Marat's answer I thought my proposal is a easier to read, and understand. But it's just a subjective view


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-06-14 15:19:37

------------

Your code can be written in another style, accelerated by numba library in a  no python mode:
```
import numba as nb

@nb.njit("float64[:, ::1](int64)", parallel=True, fastmath=True)
def createMatrix(n):
    matrix = np.empty((n, n))        # np.zeros is slower than np.empty
    for j in nb.prange(1, n + 1):
        for i in range(1, n + 1):
            matrix[j - 1, i - 1] = 1 / (i + j - 1)
    return matrix
```

This solution will be faster than [the Marat answer](https://stackoverflow.com/a/72619104/13394817) above 3 times.
 ([temporary link to colab](https://colab.research.google.com/drive/1zzEm6gMc-3u6aDil2YsFLcD0_ZwqCyTt?usp=sharing))

---


```
n = 1000
1000 loops, best of 5: 3.52 ms per loop   # Marat
1000 loops, best of 5: 1.5  ms per loop   # numba accelerated with np.zeros
1000 loops, best of 5: 1.05 ms per loop   # numba accelerated with np.empty

n = 3000
1000 loops, best of 5: 39.5 ms per loop
1000 loops, best of 5: 19.3 ms per loop
1000 loops, best of 5: 8.91 ms per loop

n = 5000
1000 loops, best of 5: 109  ms per loop
1000 loops, best of 5: 53.5 ms per loop
1000 loops, best of 5: 24.8 ms per loop
```



------------
    
    