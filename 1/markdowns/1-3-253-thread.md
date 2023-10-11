
# Post \#70452146 [Link](https://stackoverflow.com/questions/70452146/)

## How to speed up the agg of pandas groupby bins?

**Vote**: 8 (428/702) **Views**: 1232 (643/702) 

**Internal ID** \#1-3-253

Created at 2021-12-22 16:23:15

Tags: `python` `pandas` `numpy` `scipy` `scipy.stats`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have created different bins for each column and grouped the DataFrame based on these.
```
import pandas as pd
import numpy as np

np.random.seed(100)
df = pd.DataFrame(np.random.randn(100, 4), columns=['a', 'b', 'c', 'value'])

# for simplicity, I use the same bin here
bins = np.arange(-3, 4, 0.05)

df['a_bins'] = pd.cut(df['a'], bins=bins)
df['b_bins'] = pd.cut(df['b'], bins=bins)
df['c_bins'] = pd.cut(df['c'], bins=bins)
```

The output of `df.groupby(['a_bins','b_bins','c_bins']).size()` indicates the group length is 2685619.

## Calculate statistics of each group


Then, the statistics of each group are calculated like this:
```
%%timeit
df.groupby(['a_bins','b_bins','c_bins']).agg({'value':['mean']})

>>> 16.9 s ± 637 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
```


## Expected output



1. Is it possible to speed this up?
2. The quicker method should also support finding the value by inputs of a, b, and c values, like this:


```
df.groupby(['a_bins','b_bins','c_bins']).agg({'value':['mean']}).loc[(-1.72, 0.32, 1.18)]

>>> -0.252436
```



----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2021-12-22 23:04:13

------------

For this data, I'd suggest you pivot the data, and pass the mean. Usually, this is faster since you are hitting the entire dataframe, instead of going through each group:
```
(df
 .pivot(None, ['a_bins', 'b_bins', 'c_bins'], 'value')
 .mean()
 .sort_index() # ignore this if you are not fuzzy on order
)

a_bins         b_bins         c_bins       
(-2.15, -2.1]  (0.25, 0.3]    (-1.3, -1.25]    0.929100
               (0.75, 0.8]    (-0.3, -0.25]    0.480411
(-2.05, -2.0]  (-0.1, -0.05]  (0.3, 0.35]     -1.684900
               (0.75, 0.8]    (-0.25, -0.2]   -1.184411
(-2.0, -1.95]  (-0.6, -0.55]  (-1.2, -1.15]   -0.021176
                                                 ...   
(1.7, 1.75]    (-0.75, -0.7]  (1.05, 1.1]     -0.229518
(1.85, 1.9]    (-0.4, -0.35]  (1.8, 1.85]      0.003017
(1.9, 1.95]    (-1.45, -1.4]  (0.1, 0.15]      0.949361
(2.05, 2.1]    (-0.35, -0.3]  (-0.65, -0.6]    0.763184
(2.25, 2.3]    (-0.95, -0.9]  (0.1, 0.15]      2.539432
```

This matches the output from the groupby:
```
(df
 .groupby(['a_bins','b_bins','c_bins'])
 .agg({'value':['mean']})
 .dropna()
 .squeeze()
)

a_bins         b_bins         c_bins       
(-2.15, -2.1]  (0.25, 0.3]    (-1.3, -1.25]    0.929100
               (0.75, 0.8]    (-0.3, -0.25]    0.480411
(-2.05, -2.0]  (-0.1, -0.05]  (0.3, 0.35]     -1.684900
               (0.75, 0.8]    (-0.25, -0.2]   -1.184411
(-2.0, -1.95]  (-0.6, -0.55]  (-1.2, -1.15]   -0.021176
                                                 ...   
(1.7, 1.75]    (-0.75, -0.7]  (1.05, 1.1]     -0.229518
(1.85, 1.9]    (-0.4, -0.35]  (1.8, 1.85]      0.003017
(1.9, 1.95]    (-1.45, -1.4]  (0.1, 0.15]      0.949361
(2.05, 2.1]    (-0.35, -0.3]  (-0.65, -0.6]    0.763184
(2.25, 2.3]    (-0.95, -0.9]  (0.1, 0.15]      2.539432
Name: (value, mean), Length: 100, dtype: float64
```

The pivot option gives a speed of 3.72ms on my PC, while I had to terminate the groupby option, as it was taking too long (my PC is quite old :))
Again, the reason why this works/is faster is because the mean is hitting the entire dataframe, and not going through groups in the groupby.
As to your other question, you can index it easily:
```
bin_mean = (df
 .pivot(None, ['a_bins', 'b_bins', 'c_bins'], 'value')
 .mean()
 .sort_index() # ignore this if you are not fuzzy on order
)

bin_mean.loc[(-1.72, 0.32, 1.18)]
 -0.25243603652138985
```

The main problem though is Pandas for categoricals will return for all rows( which is wasteful, and not efficient); pass `observed = True` and you should notice a dramatic improvement:
```
(df.groupby(['a_bins','b_bins','c_bins'], observed=True)
   .agg({'value':['mean']})
)

                                              value
                                               mean
a_bins        b_bins        c_bins                 
(-2.15, -2.1] (0.25, 0.3]   (-1.3, -1.25]  0.929100
              (0.75, 0.8]   (-0.3, -0.25]  0.480411
(-2.05, -2.0] (-0.1, -0.05] (0.3, 0.35]   -1.684900
              (0.75, 0.8]   (-0.25, -0.2] -1.184411
(-2.0, -1.95] (-0.6, -0.55] (-1.2, -1.15] -0.021176
...                                             ...
(1.7, 1.75]   (-0.75, -0.7] (1.05, 1.1]   -0.229518
(1.85, 1.9]   (-0.4, -0.35] (1.8, 1.85]    0.003017
(1.9, 1.95]   (-1.45, -1.4] (0.1, 0.15]    0.949361
(2.05, 2.1]   (-0.35, -0.3] (-0.65, -0.6]  0.763184
(2.25, 2.3]   (-0.95, -0.9] (0.1, 0.15]    2.539432
```

Speed is about 7.39ms on my PC, about 2 times less than the pivot option, but way faster now, and that's because only categoricals that exist in the dataframe are used/returned.


------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-12-23 08:38:06

------------

An alternative straightforward solution, based on [convtools](https://github.com/westandskif/convtools), which is able to process input stream of data and doesn't require input data to fit into memory:
```
import numpy as np
import pandas as pd

from convtools import conversion as c


def c_bin(left, right, bin_size):
    return c.if_(
        c.or_(c.this < left, c.this > right),
        None,
        ((c.this - left) // bin_size).pipe(
            (c.this * bin_size + left, (c.this + 1) * bin_size + left)
        ),
    )


to_binned = c_bin(-3, 4, 0.05)
to_interval = c.if_(c.this, c.apply_func(pd.Interval, c.this, {}), None)

a_bins = c.item(0).pipe(to_binned)
b_bins = c.item(1).pipe(to_binned)
c_bins = c.item(2).pipe(to_binned)
converter = (
    c.group_by(a_bins, b_bins, c_bins)
    .aggregate(
        {
            "a_bins": a_bins.pipe(to_interval),
            "b_bins": b_bins.pipe(to_interval),
            "c_bins": c_bins.pipe(to_interval),
            "value_mean": c.ReduceFuncs.Average(c.item(3)),
        }
    )
    .gen_converter()
)


np.random.seed(100)
data = np.random.randn(100, 4)

df = pd.DataFrame(converter(data)).set_index(["a_bins", "b_bins", "c_bins"])
df.loc[(-1.72, 0.32, 1.18)]
```

Timings:
```
In [44]: %timeit converter(data)
438 µs ± 1.59 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

# passing back to pandas, timing the end-to-end thing:
In [43]: %timeit pd.DataFrame(converter(data)).set_index(["a_bins", "b_bins", "c_bins"]).loc[(-1.72, 0.32, 1.18)]
2.37 ms ± 14.4 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```

JFYI: Shortened output of `converter(data)`:
```
[
 ...,
 {'a_bins': Interval(-0.44999999999999973, -0.3999999999999999, closed='right'),
  'b_bins': Interval(0.7000000000000002, 0.75, closed='right'),
  'c_bins': Interval(-0.19999999999999973, -0.1499999999999999, closed='right'),
  'value_mean': -0.08605564337254189},
 {'a_bins': Interval(-0.34999999999999964, -0.2999999999999998, closed='right'),
  'b_bins': Interval(-0.1499999999999999, -0.09999999999999964, closed='right'),
  'c_bins': Interval(0.050000000000000266, 0.10000000000000009, closed='right'),
  'value_mean': 0.18971879197958597},
 {'a_bins': Interval(-2.05, -2.0, closed='right'),
  'b_bins': Interval(0.75, 0.8000000000000003, closed='right'),
  'c_bins': Interval(-0.25, -0.19999999999999973, closed='right'),
  'value_mean': -1.1844114274105708}]
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-22 17:37:20

------------

This is a good use-case for [scipy.stats.binned_statistic_dd](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.binned_statistic_dd.html). The snippet below computes mean statistic only, but many other statistics are supported (see docs linked above):
```
import numpy as np
import pandas as pd

np.random.seed(100)
df = pd.DataFrame(np.random.randn(100, 4), columns=["a", "b", "c", "value"])

# for simplicity, I use the same bin here
bins = np.arange(-3, 4, 0.05)

df["a_bins"] = pd.cut(df["a"], bins=bins)
df["b_bins"] = pd.cut(df["b"], bins=bins)
df["c_bins"] = pd.cut(df["c"], bins=bins)

# this takes about 35 seconds
result_pandas = df.groupby(["a_bins", "b_bins", "c_bins"]).agg({"value": ["mean"]})

from scipy.stats import binned_statistic_dd

# this takes about 20 ms
result_scipy = binned_statistic_dd(
    df[["a", "b", "c"]].to_numpy(), df["value"], bins=(bins, bins, bins)
)

# this is a verbose way to get a dataframe representation
# for many purposes this probably will not be needed
# takes about 5 seconds
temp_list = []
for na, a in enumerate(result_scipy[1][0][:-1]):
    for nb, b in enumerate(result_scipy[1][1][:-1]):
        for nc, c in enumerate(result_scipy[1][2][:-1]):
            value = result_scipy[0][na, nb, nc]
            temp_list.append([a, b, c, value])

result_scipy_as_df = pd.DataFrame(temp_list, columns=list("abcx"))

# check that the result is the same
result_scipy_as_df["x"].describe() == result_pandas["value"]["mean"].describe()
```

If you are interested in speeding up this further, this [answer](https://stackoverflow.com/a/67898951/10693596) might be useful.
An important caveat is that `binned_statistic_dd` uses bins that are closed on the right, e.g. `[0,1)`, except for the last one (refer to the Notes in the linked docs), so for consistent bin identifiers one would have to use `right=False` in [pd.cut](https://pandas.pydata.org/docs/reference/api/pandas.cut.html).
Here's a look-up example, note that here the exact bin edge location is increased by 1 to get similar result as in pandas:
```
aloc, bloc, cloc = -2.12, 0.23, -1.25
print(result_pandas.loc[(aloc, bloc, cloc)])
print(result_scipy.statistic[
    np.digitize(aloc, result_scipy.bin_edges[0][1:]),
    np.digitize(bloc, result_scipy.bin_edges[1][1:]),
    np.digitize(cloc, result_scipy.bin_edges[2][1:]),
])
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-12-22 16:39:25

------------

Because your bins are the same for your 3 columns, use `codes` from `cat` accessor:
```
%timeit df.groupby([df['a_bins'].cat.codes, df['b_bins'].cat.codes, df['c_bins'].cat.codes])['value'].mean()
1.82 ms ± 27.6 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```



------------
    
    