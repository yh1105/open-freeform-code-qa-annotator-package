
# Post \#70361517 [Link](https://stackoverflow.com/questions/70361517/)

## Python pandas - ValueError: invalid literal for int() with base 10:

**Vote**: 1 (672/702) **Views**: 4219 (541/702) 

**Internal ID** \#1-3-235

Created at 2021-12-15 09:48:31

Tags: `python` `pandas` `dataframe`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a dataframe with a column named `year` in object format. I want to convert object to int but I have this error :
> ValueError: invalid literal for int() with base 10: '2021.0'
Here is my code :
`data_h_df['year'].astype(str).astype(int)`


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-12-15 09:50:30

------------

First convert to floats, then to integers:
```
data_h_df['year'].astype(float).astype(int)
```

If there are `NaN`s use `Int64` for integers with `NaN`s:
```
data_h_df['year'].astype(float).astype('Int64')
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-07-20 09:27:04

------------

```
df['year'] = df['year'].apply(pd.to_numeric, errors='coerce').fillna(0.0)
```

The code does two things：

1. Convert all column types to numeric types, fill in NaN for errors, and fill in 0 for NaNs
2. After this operation, the column of object (the string type stored in the column) is converted to float




------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-15 10:05:10

------------

Assign 'ignore' to the 'errors' perameter.
```
>>> data_h_df['year'].astype(float).astype(int, errors='ignore')
>>> data_h_df['year']
0    2021.0
1    2022.0
2       NaN
3       2.0
dtype: float64
```



------------
    
    