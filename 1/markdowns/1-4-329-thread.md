
# Post \#68490691 [Link](https://stackoverflow.com/questions/68490691/)

## Faster way to look for a value in pandas DataFrame?

**Vote**: 6 (473/702) **Views**: 7100 (481/702) 

**Internal ID** \#1-4-329

Created at 2021-07-22 19:43:19

Tags: `python` `r` `pandas` `dataframe` `filtering`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `r` (full parsed tag list: `python` `r`)

----------

**Notepad**


----------

I'm trying to "translate" some of my R scripts to Python, but I notice, that working with data frames in Python is tremendously slower than doing it in R, e.g. exctracting cells according to some conditions.
I've done a little investigation, this is how much time it takes to look for a specific value in Python:
```
import pandas as pd
from timeit import default_timer as timer

code = 145896

# real df is way bigger
df = pd.DataFrame(data={
    'code1': [145896, 800175, 633974, 774521, 416109],
    'code2': [100, 800, 600, 700, 400],
    'code3': [1, 8, 6, 7, 4]}
    )

start = timer()
for _ in range(100000):
    desired = df.loc[df['code1']==code, 'code2'][0]
print(timer() - start) # 19.866242500000226 (sec)
```

and in R:
```
code <- 145896

df <- data.frame("code1" = c(145896, 800175, 633974, 774521, 416109),
           "code2" = c(100, 800, 600, 700, 400),
           "code3" = c(1, 8, 6, 7, 4))

start <- Sys.time()
for (i in 1:100000) {
  desired <- df[df$code1 == code, "code2"]
}
print(Sys.time() - start) # Time difference of 1.140949 secs
```

I'm relatively new to Python, and I'm probably missing something. Is there some way to speed up this process? Maybe the whole idea of transferring this script to Python is pointless? In other operations Python is faster (namely working with strings), and it would be very inconvenient to jump between two or more scripts once working with data frames is required. Any help on this, please?

Real script block iterates over rows of initial data frame (which is fairly large, 500-1500k rows) and creates a new one with rows, containing value from original column "code1" and codes, that correspond it, from another data frame, and many other values, that are newly created. I believe, I can clarify it with the picture:
[](https://i.stack.imgur.com/89h2R.png)
Later in the script I will need to search for specific values in loops based on different conditions too. So the speed of search is essential.


----------
        
## Answer \#0

**Accepted** Vote: 12

Created at 2021-07-22 20:30:06

------------

Since you are looking to select a  value from a DataFrame there are a few things you can do to improve performance.

1. Use .item() instead of [0], which has a small, but decent improvement especially for smaller DataFrames.
2. It's wasteful to mask the entire DataFrame just to then select a known Series. Instead mask only the Series and select the value. Though you might think "oh this is chained -- the forbidden ][", it's only chained assignment which is worrisome, not chained selection.
3. Use numpy. Pandas has a lot of overhead due to the indexing and alingment. But you just want to select a single value from a rectangular data structure, so dropping down to numpy will be faster.



---


Below are illustrations of the timing for different ways to select the data [Each with it's own method below]. Using `numpy` is by far the fastest, especially for a smaller DataFrame like in your sample. For those, it will be more than 20x faster than your original way to select data, which looking at your initial comparisons with R should make it  than selecting data in R. As the DataFrames get larger the relative performance of the numpy solution isn't as good, but it's still the fastest method (shown here).
```
import perfplot
import pandas as pd
import numpy as np

def DataFrame_Slice(df, code=0):
    return df.loc[df['code1'] == code, 'code2'].iloc[0]

def DataFrame_Slice_Item(df, code=0):
    return df.loc[df['code1'] == code, 'code2'].item()

def Series_Slice_Item(df, code=0):
    return df['code2'][df['code1'] == code].item()

def with_numpy(df, code=0):
    return df['code2'].to_numpy()[df['code1'].to_numpy() == code].item()


perfplot.show(
    setup=lambda N: pd.DataFrame({'code1': range(N),
                                  'code2': range(50, N+50),
                                  'code3': range(100, N+100)}),
    kernels=[
        lambda df: DataFrame_Slice(df),
        lambda df: DataFrame_Slice_Item(df),
        lambda df: Series_Slice_Item(df),
        lambda df: with_numpy(df)
    ],
    labels=['DataFrame_Slice', 'DataFrame_Slice_Item', 'Series_Slice_Item', 'with_numpy'],
    n_range=[2 ** k for k in range(1, 21)],
    equality_check=np.allclose,  
    relative_to=3,
    xlabel='len(df)'
)
```

[](https://i.stack.imgur.com/yZ8hy.png)


------------
    
    
## Answer \#1

 Vote: 6

Created at 2021-07-22 20:01:25

------------

You can cut it in about half just by reusing the filter expression.
```
In [1]: import pandas as pd

In [2]: code = 145896
   ...: df = pd.DataFrame(data={
   ...:     'code1': [145896, 800175, 633974, 774521, 416109],
   ...:     'code2': [100, 800, 600, 700, 400],
   ...:     'code3': [1, 8, 6, 7, 4]
   ...: })

In [3]: %timeit df.loc[df['code1'] == code, 'code2'][0]
197 µs ± 5.14 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

In [4]: filter_expr = df['code1'] == code

In [5]: %timeit df.loc[filter_expr, 'code2'][0]
106 µs ± 3.3 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)
```

Indexing the dataframe by the key column (assuming the lookup is frequent) should be the way to go because the dataframe's index is a hash table (see [this answer](https://stackoverflow.com/a/27238758/2072035) and [these slides](https://www.slideshare.net/wesm/a-look-at-pandas-design-and-development) for more details).
```
In [6]: df_idx = df.set_index('code1')

In [7]: %timeit df_idx.loc[code]['code2']
72.7 µs ± 1.58 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)
```

And depending on other use cases that you have, having data a true embedded (in-memory) database, SQLite or [DuckDB](https://duckdb.org/) (), may also be a solution.


------------
    
    
## Answer \#2

 Vote: 5

Created at 2022-03-22 06:31:35

------------

One way to do it is to fall back on numpy by first getting the underlying arrays and then looping:
```
import pandas as pd
from timeit import default_timer as timer

code = 145896

# real df is way bigger
df = pd.DataFrame(data={
    'code1': [145896, 800175, 633974, 774521, 416109],
    'code2': [100, 800, 600, 700, 400],
    'code3': [1, 8, 6, 7, 4]}
    )

start = timer()


code1 = df['code1'].values
code2 = df['code2'].values
code3 = df['code3'].values

for _ in range(100000):
    desired = code1 == code
    desired_code2 = code2[desired][0]
    desired_code3 = code3[desired][0]

print(timer() - start) # 0.26 (sec)
```

The following code adapted from [ALollz](https://stackoverflow.com/users/4333359/alollz)'s answer illustrates the difference in performance of the different techniques with several more added and an increased dataset size range that shows changing behavior affected by orders of magnitude.
```
import perfplot
import pandas as pd
import numpy as np

def DataFrame_Slice(df, code=0):
    return df.loc[df['code1'] == code, 'code2'].iloc[0]

def DataFrame_Slice_Item(df, code=0):
    return df.loc[df['code1'] == code, 'code2'].item()

def Series_Slice_Item(df, code=0):
    return df['code2'][df['code1'] == code].item()

def with_numpy(df, code=0):
    return df['code2'].to_numpy()[df['code1'].to_numpy() == code].item()

def with_numpy_values(code1, code2, code=0):
    desired = code1 == code
    desired_code2 = code2[desired][0]
    return desired_code2

def DataFrameIndex(df, code=0):
    return df.loc[code].code2

def with_numpy_argmax(code1, code2, code=0):
    return code2[np.argmax(code1==code)]

def numpy_search_sorted(code1, code2, sorter, code=0):
    return code2[sorter[np.searchsorted(code1, code, sorter=sorter)]]

def python_dict(code1_dict, code2, code=0):
    return code2[code1_dict[code]]

def shuffled_array(N):
    a = np.arange(0, N)
    np.random.shuffle(a)
    return a

def setup(N):
    print(f'setup {N}')
    df = pd.DataFrame({'code1': shuffled_array(N),
                                  'code2': shuffled_array(N),
                                  'code3': shuffled_array(N)})
    
    code = df.iloc[min(len(df)//2, len(df)//2 + 20)].code1
    
    sorted_index = df['code1'].values.argsort()
    
    code1 = df['code1'].values
    code2 = df['code2'].values
    
    code1_dict = {code1[i]: i for i in range(len(code1))}
    
    return df, df.set_index('code1'), code1, code2, sorted_index, code1_dict, code


for relative_to in [5, 6, 7]:
    perfplot.show(
        setup=setup,
        kernels=[
            lambda df, _, __, ___, sorted_index, ____, code: DataFrame_Slice(df, code),
            lambda df, _, __, ___, sorted_index, ____, code: DataFrame_Slice_Item(df, code),
            lambda df, _, __, ___, sorted_index, ____, code: Series_Slice_Item(df, code),
            lambda df, _, __, ___, sorted_index, ____, code: with_numpy(df, code),
            lambda _, __, code1, code2, sorted_index, ____,  code: with_numpy_values(code1, code2, code),
            lambda _, __, code1, code2, sorted_index, ____, code: with_numpy_argmax(code1, code2, code),
            lambda _, df, __, ___, sorted_index, ____, code: DataFrameIndex(df, code),
            lambda _, __, code1, code2, search_index, ____, code: numpy_search_sorted(code1, code2, search_index, code),
            lambda _, __, ___, code2, _____, code1_dict, code: python_dict(code1_dict, code2, code)
        ],
        logy=True,
        labels=['DataFrame_Slice', 'DataFrame_Slice_Item', 'Series_Slice_Item', 'with_numpy', 'with_numpy_values', 'with_numpy_argmax', 'DataFrameIndex', 'numpy_search_sorted', 'python_dict'],
        n_range=[2 ** k for k in range(1, 25)],
        equality_check=np.allclose,  
        relative_to=relative_to,
        xlabel='len(df)')
```

Conclusion: Numpy search sorted is the best performing technique except for very small data sets below 100 mark. Sequential search using the underlying `numpy` arrays is the next best option for datasets roughly below 100,000 after which the best option is to use the `DataFrame` index. However, when hitting the multi-million records mark, `DataFrame` indexes no longer perform well, probably due to hashing collisions.
[EDIT 03/24/2022]
Using a Python dictionary beats all other techniques by at least one order of magnitude.
[](https://i.stack.imgur.com/4TqEZ.png)
[](https://i.stack.imgur.com/Aw4nR.png)
[](https://i.stack.imgur.com/bkDta.png)
Note: We are assuming repeated searches within the DataFrame, so as to offset the cost of acquiring the underlying numpy arrays, indexing the DataFrame or sorting the numpy array.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-07-22 20:44:51

------------

R-lang was probably designed around this kind of operations. Pandas is a third-party library in python, so it has additional limitations and overheads to work around. Namely, upon each intermediate step a new dataframe or series is generated. Almost each equals sign or a bracket is an intermediate step.
If you really want to extract single element at a time from a single column, you can try setting index:
```
df2 = df.set_index('code1')
def getel(df2,code):
    desired = None
    if code in df2.index: 
        desired = df2['code2'][code]
        if isinstance(desired, pd.Series):
            desired = desired.iloc[0]
    return code
```

This is three times faster than original if the value is duplicated. If the value is unique, then `desired = df2['code2'][code]` does not generate new series and the code is 17 times faster than original. Also, notice that selecting columns before everything else tends to be a notable reduction in the unnecessary work pandas do.
If you instead want to do similar operations on different values, then you should probably look at `groupby`. Or at least filter all the values to be processed at once:
```
codes = {145896,774521}
pad = df['code1'].apply(lambda x: x in codes) #this is good when there are many codes
#or
pad = df['code1'].isin(codes) #this is linear time in len(codes)
result = df[pad].apply(do_stuff, axis = 1)
#or df[pad].transform(do_stuff, axis = 1)
```



------------
    
    