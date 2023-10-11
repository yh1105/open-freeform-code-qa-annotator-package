
# Post \#72449735 [Link](https://stackoverflow.com/questions/72449735/)

## How to apply a condition on the entire dataframe/ all columns

**Vote**: 1 (672/702) **Views**: 1040 (657/702) 

**Internal ID** \#1-3-311

Created at 2022-05-31 14:33:54

Tags: `python` `pandas`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a dataframe with 14 rows × 1500 columns which contains only Numerical values. I want to apply a simple condition that if any values in the entire dataframe is above a certain number, say 25, then replace those values with 1 else replace with 0. I have found some solution on where I can perform such operation but I have specify the column name but I couldn't find any solution where I can perform one condition on the entire dataframe.
```
df[0.0] = df[0.0].apply(lambda x: 1 if x >=25 else 0)
```

This works for a specific column but
```
df = df[:,:].apply(lambda x: 1 if x >=25 else 0)
```

doesn't work. Could someone help?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-05-31 14:37:35

------------

You might use [pandas.DataFrame.applymap](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.applymap.html) to apply function to each element following way
```
import pandas as pd
df = pd.DataFrame({'col1':[0,10,100],'col2':[0,50,500]})
df2 = df.applymap(lambda x: 1 if x >=25 else 0)
print(df2)
```

output
```
col1  col2
0     0     0
1     0     1
2     1     1
```

in this particular case you might get `df2` other way namely
```
df2 = (df >= 25).astype(int)
```

it does first create `pandas.DataFrame` of booleans then convert it to `int`s (`False` to `0`, `True` to `1`)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2022-05-31 14:41:39

------------

You can try
```
df = np.where(df >= 25, 1, 0)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-05-31 14:39:55

------------

The function associated with `applymap()` is applied to all the elements of the given DataFrame, and hence the `applymap()` method is defined for DataFrames only.
Similarly, the function associated with the `apply()` method can be applied to all the elements of DataFrame or Series, and hence the `apply()` method is defined for both Series and DataFrame objects.
The `map()` method can only be defined for Series objects in Pandas.
So, you have to use `applymap()` instead of `apply()`.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-05-31 14:42:02

------------

This should let you dynamically find where x > 25 and set a new with the same column names
```
data = {
    'Column1' : [1, 26, 3, 27],
    'Column2' : [25, 26, 1, 1]
}

df = pd.DataFrame(data)
df_new = pd.DataFrame(np.where(df > 25, 1, 0), columns = df.columns)
df_new
```



------------
    
    