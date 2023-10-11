
# Post \#71426929 [Link](https://stackoverflow.com/questions/71426929/)

## Python: sum values in column where condition is met

**Vote**: 2 (631/702) **Views**: 1081 (654/702) 

**Internal ID** \#1-3-211

Created at 2022-03-10 15:44:14

Tags: `python` `pandas` `dataframe` `filter` `pandas-groupby`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a DataFrame like this:
```
import pandas as pd

df=pd.DataFrame()
df['exchange'] = [1, 1, 1, 2, 3]
df['type'] = ['deposit', 'deposit', 'trade', 'deposit', 'deposit']
df['value'] = [10, 10, '30', '40', '100']
```

which looks like:
```
exchange     type       value
 0  1            deposit    10
 1  1            deposit    10
 2  1            trade      30
 3  2            deposit    40
 4  3            deposit    100
```

I want to add the elements in the `"value"` column where `"type"='deposit'` based on the `"exchange"` and forward-fill to get something like this:
```
exchange     type       value   balance
 0  1            deposit    10      10
 1  1            deposit    10      20
 2  1            trade      30      20
 3  2            deposit    40      40
 4  3            deposit    100     100
```

where `"balance"` is the sum of `deposits` filtered by `"exchange"`.
Is there a way to do this pythonically without for loops/if statements?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-03-10 15:54:58

------------

You can first group by "exchange", then apply `np.cumsum` and finally assign the result where `type` is "deposit".
```
import pandas as pd
import numpy as np

df.loc[df["type"]=="deposit", "balance"] = df.loc[df["type"]=="deposit"].groupby("exchange", sort=False)["value"].apply(np.cumsum)
```

Finally you can fill missing value with the forward-fill as you have mentioned.
```
df = df.fillna(method='ffill')
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-03-10 15:58:32

------------

You could use `groupby` + `cumsum` to fill in "balance" column; this fills in for "trade"s as well, so you `mask` the result depending on with `type` is "deposit" or not and forward fill using `ffill` if it's not "deposit":
```
df['value'] = df['value'].astype(int)
df['balance'] = df['value'].mask(df['type']!='deposit').groupby(df['exchange']).cumsum().ffill()
```

Output:
```
exchange     type  value  balance
0         1  deposit     10     10.0
1         1  deposit     10     20.0
2         1    trade     30     20.0
3         2  deposit     40     40.0
4         3  deposit    100    100.0
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-10 16:02:18

------------

You use `where` to `NaN` non deposit rows and then use an `expanding` `sum`, within each exhange group as it considers `NaN` 0 when summing so it winds up forward filling just as you want.
```
df['balance'] = (df['value'].where(df['type'].eq('deposit'))
                   .groupby(df['exchange'])
                   .expanding().sum()
                   .reset_index(0, drop=True))

   exchange     type  value  balance
0         1  deposit     10     10.0
1         1  deposit     10     20.0
2         1    trade     30     20.0
3         2  deposit     40     40.0
4         3  deposit    100    100.0
```



------------
    
    