
# Post \#72452854 [Link](https://stackoverflow.com/questions/72452854/)

## How to sum across n elements of numpy array

**Vote**: 1 (672/702) **Views**: 1212 (646/702) 

**Internal ID** \#1-3-304

Created at 2022-05-31 18:54:25

Tags: `python` `arrays` `numpy` `sum`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I hope that someone can help me with my problem since I'm not used to python and numpy yet. I have the following array with 24 elements:
```
load = np.array([10, 12, 9, 13, 17, 23, 25, 28, 26, 24, 22, 20, 18, 20, 22, 24, 26, 28, 23, 24, 21, 18, 16, 13])
```

I want to create a new array with the same length as "load" and calculate for each element in the array the sum of the current and the next two numbers, so that my objective array would look like this:
```
[31, 34, 39, 53, 65, 76, 79, 78, 72, 66, 60, 58, 60, 66, 72, 78, 77, 75, 68, 63, 55, 47, 29, 13]
```

I tried to solve this with the following code:
```
output = np.empty(len(load))
for i in range((len(output))-2):
    output[i] = load[i]+load[i+1]+load[i+2]
print(output)
```

The output array looks like this:
```
array([31. , 34. , 39. , 53. , 65. , 76. , 79. , 78. , 72. , 66. , 60. ,
       58. , 60. , 66. , 72. , 78. , 77. , 75. , 68. , 63. , 55. , 47. ,
        6. ,  4.5])
```

The last two numbers are not right. For the 23th element I want the sum of just 16 and 13 and for the last number to stay 13 since the array ends there. I don't unterstand how python calculated these numbers. Also I would prefer the numbers to be integers without the dot.
Does anyone have a better solution in mind? I know that this probably is easy to solve, I just don't know all the functionalities of numpy.
Thank you very much!


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-05-31 19:17:02

------------

[np.empty](https://numpy.org/doc/stable/reference/generated/numpy.empty.html) creates an array containing uninitialized data. In your code, you initialize an array `output` of length 24 but assign only 22 values to it. The last 2 values contain arbitrary values (i.e. garbage). Unless performance is of importance, `np.zeros` is usually the better choice for initializing arrays since all values will have a consistent value of 0.
You can solve this without a for loop by padding the input array with zeros, then computing a vectorized sum.
```
import numpy as np

load = np.array([10, 12, 9, 13, 17, 23, 25, 28, 26, 24, 22, 20, 18, 20, 22, 24, 26, 28, 23, 24, 21, 18, 16, 13])
tmp = np.pad(load, [0, 2])
output = load + tmp[1:-1] + tmp[2:]

print(output)
```

Output
```
[31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-05-31 18:57:25

------------

If the array is not super long, and you don't care too much about memory utilization you could use:
```
from itertools import zip_longest

output = [sum([x, y, z]) for x, y, z in zip_longest(load, load[1:], load[2:], fillvalue=0)]
```

Output is:
```
[31, 34, 39, 53, 65, 76, 79, 78, 72, 66, 60, 58, 60, 66, 72, 78, 77, 75, 68, 63, 55, 47, 29, 13]
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-06-01 14:45:16

------------

I'll address the question "How Python calculated those two numbers" in your source: they were not calculated by your program.
If you notice, your main loop runs until the end of the array but the last two elements. The value of those was not touched. For this reason it corresponds to the data that was in the memory at the position corresponding to the memory allocated by [np.empty()](https://numpy.org/doc/stable/reference/generated/numpy.empty.html). In fact, `np.empty()` will only acquire the ownership of the memory without initialization (i.e. without changing its content).

---


A simple approach is to loop through and sum different views of the original array:
```
def sum_next_k_loop(arr, k):
    result = arr.copy()
    for i in range(1, k):
        result[:-i] += arr[i:]
    return result
```

This is quite fast for relatively small values of `k`, but as `k` gets larger one may want to avoid the relatively slow explicit looping.
One way to do this is to use strides to create a view of the array that can be used to sum along an extra dimension.
This approach leaves behind the partial sums at the end of the input.
One could either start with a zero-padded input:
```
import numpy as np
import numpy.lib.stride_tricks


def sum_next_k_strides(arr, k):
    n = arr.size
    result = np.zeros(arr.size + k - 1, dtype=arr.dtype)
    result[:n] = arr
    window = (k,) * result.ndim
    window_size = k ** result.ndim
    reduced_shape = tuple(dim - k + 1 for dim, k in zip(result.shape, window))
    view = np.lib.stride_tricks.as_strided(
        result, shape=reduced_shape + window, strides=arr.strides * 2, writeable=False)
    result = np.sum(view, axis=-1)
    return result
```

or, more memory efficiently, construct the tail afterwards with `np.cumsum()`:
```
import numpy as np
import numpy.lib.stride_tricks


def sum_next_k_strides_cs(arr, k):
    n = arr.size
    window = (k,) * arr.ndim
    window_size = k ** arr.ndim
    reduced_shape = tuple(dim - k + 1 for dim, k in zip(arr.shape, window))
    view = np.lib.stride_tricks.as_strided(
        arr, shape=reduced_shape + window, strides=arr.strides * 2, writeable=False)
    result = np.empty_like(arr)
    result[:n - k + 1] = np.sum(view, axis=-1)
    result[n - k:] = np.cumsum(arr[-1:-(k + 1):-1])[::-1]
    return result
```

Note that looping through the input size instead of `k` is not going to be fast, no matter the inputs, because `k` is limited by the size of the input.
Alternatively, one could use `np.convolve()`, which computes exactly what you are after but with both tails, so that you just need to slice out the starting tail:
```
def sum_next_k_conv(arr, k):
    return np.convolve(arr, (1,) * k)[(k - 1):]
```

Finally, one could write a fully explicit looping solution accelerated with Numba:
```
import numpy as np
import numba as nb


@nb.njit
def running_sum_nb(arr, k):
    n = arr.size
    m = n - k + 1
    o = k - 1
    result = np.zeros(n, dtype=arr.dtype)
    # : fill bulk
    for j in range(m):
        tot = arr[j]
        for i in range(1, k):
            tot += arr[j + i]
        result[0 + j] = tot
    # : fill tail
    for j in range(o):
        tot = 0
        for i in range(j, o):
            tot += arr[m + i]
        result[m + j] = tot
    return result
```

To check that all the solutions give the same result as the expected output:
```
funcs = running_sum_loop, running_sum_strides, running_sum_strides_cs, running_sum_conv, running_sum_nb

load = np.array([10, 12, 9, 13, 17, 23, 25, 28, 26, 24, 22, 20, 18, 20, 22, 24, 26, 28, 23, 24, 21, 18, 16, 13])
tgt = np.array([31, 34, 39, 53, 65, 76, 79, 78, 72, 66, 60, 58, 60, 66, 72, 78, 77, 75, 68, 63, 55, 47, 29, 13])

print(f"{'Input':>24}  {load}")
print(f"{'Target':>24}  {tgt}")
for i, func in enumerate(funcs, 1):
    print(f"{func.__name__:>24}  {func(load, 3)}")
```

```
Input  [10 12  9 13 17 23 25 28 26 24 22 20 18 20 22 24 26 28 23 24 21 18 16 13]
                  Target  [31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
        running_sum_loop  [31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
  running_sum_strides_cs  [31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
     running_sum_strides  [31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
        running_sum_conv  [31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
          running_sum_nb  [31 34 39 53 65 76 79 78 72 66 60 58 60 66 72 78 77 75 68 63 55 47 29 13]
```

Benchmarking all these for varying input size:
```
import pandas as pd


timeds_n = {}
for p in range(6):
    n = 10 ** p
    k = 3
    arr = np.array(load.tolist() * n)
    print(f"N = {n * len(load)}")
    base = funcs[0](arr, k)
    timeds_n[n] = []
    for func in funcs:
        res = func(arr, k)
        timed = %timeit -r 8 -n 8 -q -o func(arr, k)
        timeds_n[n].append(timed.best)
        print(f"{func.__name__:>24}  {np.allclose(base, res)}  {timed.best:.9f}")


pd.DataFrame(data=timeds_n, index=[func.__name__ for func in funcs]).transpose().plot()
```

[](https://i.stack.imgur.com/QzKsg.png)
and varying `k`:
```
timeds_k = {}
for p in range(1, 10):
    n = 10 ** 5
    k = 2 ** p
    arr = np.array(load.tolist() * n)
    print(f"k = {k}")
    timeds_k[k] = []
    base = funcs[0](arr, k)
    for func in funcs:
        res = func(arr, k)
        timed = %timeit -q -o func(arr, k)
        timeds_k[k].append(timed.best)
        print(f"{func.__name__:>24}  {np.allclose(base, res)}  {timed.best:.9f}")


pd.DataFrame(data=timeds_k, index=[func.__name__ for func in funcs]).transpose().plot()
```

[](https://i.stack.imgur.com/405zI.png)


------------
    
    