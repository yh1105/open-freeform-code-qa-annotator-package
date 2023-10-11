
# Post \#54912626 [Link](https://stackoverflow.com/questions/54912626/)

## Python renaming Pandas DataFrame Columns

**Vote**: 14 (336/702) **Views**: 48243 (169/702) 

**Internal ID** \#1-3-204

Created at 2019-02-27 18:56:37

Tags: `python` `pandas` `dataframe` `rename`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

```
import pandas as pd
import numpy as np
datain = np.loadtxt(datafile)
df = pd.DataFrame(data = datain, columns = ["t","p","x","y","z"])
avg = df.groupby(["t"], sort=False)["p"].mean().rename(columns={1:"mean"})
```


This doesn't work, it tells me TypeError: rename() got an unexpected keyword argument "columns". It also doesn't work if I do this,

```
avg.rename(columns = {1:"mean"}, inplace=True)
```


I cannot figure out why, all documentation tells me that my columns call is correct. I just want to rename the blank column created by my "mean" call to have a string index. Anyone know why or how to fix this? All examples I've seen follow this format. Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2019-02-27 19:09:15

------------

IIUC you could do this

```
import pandas as pd
df = pd.DataFrame({"a":np.arange(10),
                   "b":np.random.choice(["A","B"],10)})

avg = df.groupby("b", sort=False)["a"].mean()\
        .reset_index(name="mean")
```


or  

```
avg = df.groupby("b", sort=False)["a"].mean().reset_index()\
        .rename(columns={"a":"mean"})
```


or 

```
avg = df.groupby("b", sort=False, as_index=False)["a"].mean()\
        .reset_index()\
        .rename(columns={"a":"mean"})
```



------------
    
    
## Answer \#1

 Vote: 8

Created at 2020-06-26 02:16:20

------------

I ran into this same problem and was also confused about what the issue was. When you call:
`df.groupby(...)["p"]....rename(columns={1:"mean"})`
the `rename()` is called on `DataFrame["p"]` which returns a Series object, not a DataFrame object. The `rename()` function for a Series object has no column parameter (because there's only 1 "column"). Sometimes, pandas will implicitly convert Series objects to DataFrames so its easy to miss. You could alternatively write
`pd.Series.to_frame(df.groupby(...)["p"].mean().reset_index(), name='mean')`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-02-27 19:27:57

------------

I think this should work:

```
avg = df.groupby(["t"], sort=False)["p"].mean().rename('mean').reset_index()
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-11-03 15:49:24

------------

I think the problem comes from the fact that when you called:
```
avg = df.groupby("b", sort=False)["a"].mean().reset_index().rename(columns={"a":"mean"})
```

This line:
```
avg = df.groupby("b", sort=False)["a"].mean().reset_index()
```

returns a `pd.Series`, not a `pd.DataFrame`. Normally if you drop the parameters of the column it should work:
```
avg = df.groupby("b", sort=False)["a"].mean().reset_index().rename("mean")
```



------------
    
    