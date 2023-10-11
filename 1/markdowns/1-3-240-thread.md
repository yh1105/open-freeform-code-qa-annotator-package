
# Post \#48647534 [Link](https://stackoverflow.com/questions/48647534/)

## Find difference between two data frames

**Vote**: 237 (11/702) **Views**: 359941 (15/702) 

**Internal ID** \#1-3-240

Created at 2018-02-06 16:25:41

Tags: `python` `pandas` `dataframe`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have two data frames df1 and df2, where df2 is a subset of df1. How do I get a new data frame (df3) which is the difference between the two data frames?

In other word, a data frame that has all the rows/columns in df1 that are not in df2?

[](https://i.stack.imgur.com/aOCGb.png)


----------
        
## Answer \#0

**Accepted** Vote: 305

Created at 2018-02-06 16:39:36

------------

By using `drop_duplicates`
```
pd.concat([df1,df2]).drop_duplicates(keep=False)
```


---


`Update :`
> `The above method only works for those data frames that don't already have duplicates themselves. For example:`
```
df1=pd.DataFrame({'A':[1,2,3,3],'B':[2,3,4,4]})
df2=pd.DataFrame({'A':[1],'B':[2]})
```

It will output like below , which is wrong
> Wrong Output :
```
pd.concat([df1, df2]).drop_duplicates(keep=False)
Out[655]: 
   A  B
1  2  3
```

> Correct Output
```
Out[656]: 
   A  B
1  2  3
2  3  4
3  3  4
```


---


> How to achieve that?
Method 1: Using `isin` with `tuple`
```
df1[~df1.apply(tuple,1).isin(df2.apply(tuple,1))]
Out[657]: 
   A  B
1  2  3
2  3  4
3  3  4
```

Method 2: `merge` with `indicator`
```
df1.merge(df2,indicator = True, how='left').loc[lambda x : x['_merge']!='both']
Out[421]: 
   A  B     _merge
1  2  3  left_only
2  3  4  left_only
3  3  4  left_only
```



------------
    
    
## Answer \#1

 Vote: 71

Created at 2018-02-06 16:33:52

------------

For rows, try this, where `Name` is the joint index column (can be a list for multiple common columns, or specify `left_on` and `right_on`):

```
m = df1.merge(df2, on='Name', how='outer', suffixes=['', '_'], indicator=True)
```


The `indicator=True` setting is useful as it adds a column called `_merge`, with all changes between `df1` and `df2`, categorized into 3 possible kinds: "left_only", "right_only" or "both".

For columns, try this:

```
set(df1.columns).symmetric_difference(df2.columns)
```



------------
    
    
## Answer \#2

 Vote: 22

Created at 2019-04-02 15:59:07

------------

[Accepted answer](https://stackoverflow.com/a/48647840/6451573) Method 1 will not work for data frames with NaNs inside, as `pd.np.nan != pd.np.nan`. I am not sure if this is the best way, but it can be avoided by
```
df1[~df1.astype(str).apply(tuple, 1).isin(df2.astype(str).apply(tuple, 1))]
```

It's slower, because it needs to cast data to string, but thanks to this casting `pd.np.nan == pd.np.nan`.
Let's go trough the code. First we cast values to string, and apply `tuple` function to each row.
```
df1.astype(str).apply(tuple, 1)
df2.astype(str).apply(tuple, 1)
```

Thanks to that, we get `pd.Series` object with list of tuples. Each tuple contains whole row from `df1`/`df2`.
Then we apply `isin` method on `df1` to check if each tuple "is in" `df2`.
The result is `pd.Series` with bool values. True if tuple from `df1` is in `df2`. In the end, we negate results with `~` sign, and applying filter on `df1`. Long story short, we get only those rows from `df1` that are not in `df2`.
To make it more readable, we may write it as:
```
df1_str_tuples = df1.astype(str).apply(tuple, 1)
df2_str_tuples = df2.astype(str).apply(tuple, 1)
df1_values_in_df2_filter = df1_str_tuples.isin(df2_str_tuples)
df1_values_not_in_df2 = df1[~df1_values_in_df2_filter]
```



------------
    
    
## Answer \#3

 Vote: 15

Created at 2018-06-07 17:59:37

------------

```
import pandas as pd
# given
df1 = pd.DataFrame({'Name':['John','Mike','Smith','Wale','Marry','Tom','Menda','Bolt','Yuswa',],
    'Age':[23,45,12,34,27,44,28,39,40]})
df2 = pd.DataFrame({'Name':['John','Smith','Wale','Tom','Menda','Yuswa',],
    'Age':[23,12,34,44,28,40]})

# find elements in df1 that are not in df2
df_1notin2 = df1[~(df1['Name'].isin(df2['Name']) & df1['Age'].isin(df2['Age']))].reset_index(drop=True)

# output:
print('df1\n', df1)
print('df2\n', df2)
print('df_1notin2\n', df_1notin2)

# df1
#     Age   Name
# 0   23   John
# 1   45   Mike
# 2   12  Smith
# 3   34   Wale
# 4   27  Marry
# 5   44    Tom
# 6   28  Menda
# 7   39   Bolt
# 8   40  Yuswa
# df2
#     Age   Name
# 0   23   John
# 1   12  Smith
# 2   34   Wale
# 3   44    Tom
# 4   28  Menda
# 5   40  Yuswa
# df_1notin2
#     Age   Name
# 0   45   Mike
# 1   27  Marry
# 2   39   Bolt
```



------------
    
    
## Answer \#4

 Vote: 8

Created at 2019-05-03 16:33:22

------------

edit2, I figured out a new solution without the need of setting index
```
newdf=pd.concat([df1,df2]).drop_duplicates(keep=False)
```

Okay i found the answer of highest vote already contain what I have figured out. Yes, we can only use this code on condition that there are no duplicates in each two dfs.

---


I have a tricky method. First we set ’Name’ as the index of two dataframe given by the question. Since we have same ’Name’ in two dfs, we can just drop the ’smaller’ df’s index from the ‘bigger’ df.
Here is the code.
```
df1.set_index('Name',inplace=True)
df2.set_index('Name',inplace=True)
newdf=df1.drop(df2.index)
```



------------
    
    
## Answer \#5

 Vote: 8

Created at 2019-06-04 18:46:38

------------

Perhaps a simpler one-liner, with identical or different column names. Worked even when df2['Name2'] contained duplicate values.

```
newDf = df1.set_index('Name1')
           .drop(df2['Name2'], errors='ignore')
           .reset_index(drop=False)
```



------------
    
    
## Answer \#6

 Vote: 8

Created at 2022-04-20 06:55:58

------------

Pandas now offers a [new API](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.compare.html) to do data frame diff: `pandas.DataFrame.compare`
```
df.compare(df2)
  col1       col3
  self other self other
0    a     c  NaN   NaN
2  NaN   NaN  3.0   4.0
```



------------
    
    
## Answer \#7

 Vote: 3

Created at 2019-09-04 19:07:55

------------

In addition to accepted answer, I would like to propose one more wider solution that can find a  of two dataframes with any `index`/`columns` (they might not coincide for both datarames). Also method allows to setup tolerance for `float` elements for dataframe comparison (it uses `np.isclose`)

```
import numpy as np
import pandas as pd

def get_dataframe_setdiff2d(df_new: pd.DataFrame, 
                            df_old: pd.DataFrame, 
                            rtol=1e-03, atol=1e-05) -> pd.DataFrame:
    """Returns set difference of two pandas DataFrames"""

    union_index = np.union1d(df_new.index, df_old.index)
    union_columns = np.union1d(df_new.columns, df_old.columns)

    new = df_new.reindex(index=union_index, columns=union_columns)
    old = df_old.reindex(index=union_index, columns=union_columns)

    mask_diff = ~np.isclose(new, old, rtol, atol)

    df_bool = pd.DataFrame(mask_diff, union_index, union_columns)

    df_diff = pd.concat([new[df_bool].stack(),
                         old[df_bool].stack()], axis=1)

    df_diff.columns = ["New", "Old"]

    return df_diff
```


Example:

```
In [1]

df1 = pd.DataFrame({'A':[2,1,2],'C':[2,1,2]})
df2 = pd.DataFrame({'A':[1,1],'B':[1,1]})

print("df1:\n", df1, "\n")

print("df2:\n", df2, "\n")

diff = get_dataframe_setdiff2d(df1, df2)

print("diff:\n", diff, "\n")
```


```
Out [1]

df1:
   A  C
0  2  2
1  1  1
2  2  2 

df2:
   A  B
0  1  1
1  1  1 

diff:
     New  Old
0 A  2.0  1.0
  B  NaN  1.0
  C  2.0  NaN
1 B  NaN  1.0
  C  1.0  NaN
2 A  2.0  NaN
  C  2.0  NaN
```



------------
    
    
## Answer \#8

 Vote: 3

Created at 2020-03-11 06:38:28

------------

As mentioned [here](https://stackoverflow.com/a/48647840/5350456)
that 

```
df1[~df1.apply(tuple,1).isin(df2.apply(tuple,1))]
```


is correct solution but it will produce wrong output if

```
df1=pd.DataFrame({'A':[1],'B':[2]})
df2=pd.DataFrame({'A':[1,2,3,3],'B':[2,3,4,4]})
```


In that case above solution will give
, instead you should use `concat` method after removing duplicates from each datframe.

Use `concate with drop_duplicates`

```
df1=df1.drop_duplicates(keep="first") 
df2=df2.drop_duplicates(keep="first") 
pd.concat([df1,df2]).drop_duplicates(keep=False)
```



------------
    
    
## Answer \#9

 Vote: 2

Created at 2021-03-07 12:18:37

------------

I had issues with handling duplicates when there were duplicates on one side and at least one on the other side, so I used `Counter.collections` to do a better diff, ensuring both sides have the same count.  This doesn't return duplicates, but it won't return any if both sides have the same count.
```
from collections import Counter

def diff(df1, df2, on=None):
    """
    :param on: same as pandas.df.merge(on) (a list of columns)
    """
    on = on if on else df1.columns
    df1on = df1[on]
    df2on = df2[on]
    c1 = Counter(df1on.apply(tuple, 'columns'))
    c2 = Counter(df2on.apply(tuple, 'columns'))
    c1c2 = c1-c2
    c2c1 = c2-c1
    df1ondf2on = pd.DataFrame(list(c1c2.elements()), columns=on)
    df2ondf1on = pd.DataFrame(list(c2c1.elements()), columns=on)
    df1df2 = df1.merge(df1ondf2on).drop_duplicates(subset=on)
    df2df1 = df2.merge(df2ondf1on).drop_duplicates(subset=on)
    return pd.concat([df1df2, df2df1])
```

```
> df1 = pd.DataFrame({'a': [1, 1, 3, 4, 4]})
> df2 = pd.DataFrame({'a': [1, 2, 3, 4, 4]})
> diff(df1, df2)
   a
0  1
0  2
```



------------
    
    
## Answer \#10

 Vote: 2

Created at 2022-11-19 20:52:16

------------

There is a new method in pandas [DataFrame.compare](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.compare.html) that compare 2 different dataframes and return which values changed in each column for the data records.

### Example


First Dataframe
```
Id Customer Status      Date
1      ABC   Good  Mar 2023
2      BAC   Good  Feb 2024
3      CBA    Bad  Apr 2022
```

Second Dataframe
```
Id Customer Status      Date
1      ABC    Bad  Mar 2023
2      BAC   Good  Feb 2024
5      CBA   Good  Apr 2024
```


```
print("Dataframe difference -- \n")
print(df1.compare(df2))

print("Dataframe difference keeping equal values -- \n")
print(df1.compare(df2, keep_equal=True))

print("Dataframe difference keeping same shape -- \n")
print(df1.compare(df2, keep_shape=True))

print("Dataframe difference keeping same shape and equal values -- \n")
print(df1.compare(df2, keep_shape=True, keep_equal=True))
```


```
Dataframe difference -- 

    Id       Status            Date          
  self other   self other      self     other
0  NaN   NaN   Good   Bad       NaN       NaN
2  3.0   5.0    Bad  Good  Apr 2022  Apr 2024

Dataframe difference keeping equal values -- 

    Id       Status            Date          
  self other   self other      self     other
0    1     1   Good   Bad  Mar 2023  Mar 2023
2    3     5    Bad  Good  Apr 2022  Apr 2024

Dataframe difference keeping same shape -- 

    Id       Customer       Status            Date          
  self other     self other   self other      self     other
0  NaN   NaN      NaN   NaN   Good   Bad       NaN       NaN
1  NaN   NaN      NaN   NaN    NaN   NaN       NaN       NaN
2  3.0   5.0      NaN   NaN    Bad  Good  Apr 2022  Apr 2024

Dataframe difference keeping same shape and equal values -- 

    Id       Customer       Status            Date          
  self other     self other   self other      self     other
0    1     1      ABC   ABC   Good   Bad  Mar 2023  Mar 2023
1    2     2      BAC   BAC   Good  Good  Feb 2024  Feb 2024
2    3     5      CBA   CBA    Bad  Good  Apr 2022  Apr 2024
```



------------
    
    
## Answer \#11

 Vote: 1

Created at 2019-05-22 16:07:52

------------

A slight variation of the nice @liangli's solution that does not require to change the index of existing dataframes:

```
newdf = df1.drop(df1.join(df2.set_index('Name').index))
```



------------
    
    
## Answer \#12

 Vote: 1

Created at 2019-08-25 02:56:06

------------

Finding difference by index. Assuming df1 is a subset of df2 and the indexes are carried forward when subsetting

```
df1.loc[set(df1.index).symmetric_difference(set(df2.index))].dropna()

# Example

df1 = pd.DataFrame({"gender":np.random.choice(['m','f'],size=5), "subject":np.random.choice(["bio","phy","chem"],size=5)}, index = [1,2,3,4,5])

df2 =  df1.loc[[1,3,5]]

df1

 gender subject
1      f     bio
2      m    chem
3      f     phy
4      m     bio
5      f     bio

df2

  gender subject
1      f     bio
3      f     phy
5      f     bio

df3 = df1.loc[set(df1.index).symmetric_difference(set(df2.index))].dropna()

df3

  gender subject
2      m    chem
4      m     bio
```



------------
    
    
## Answer \#13

 Vote: 1

Created at 2021-10-22 13:00:08

------------

Defining our dataframes:
```
df1 = pd.DataFrame({
    'Name':
        ['John','Mike','Smith','Wale','Marry','Tom','Menda','Bolt','Yuswa'],
    'Age':
        [23,45,12,34,27,44,28,39,40]
})

df2 = df1[df1.Name.isin(['John','Smith','Wale','Tom','Menda','Yuswa'])

df1

    Name  Age
0   John   23
1   Mike   45
2  Smith   12
3   Wale   34
4  Marry   27
5    Tom   44
6  Menda   28
7   Bolt   39
8  Yuswa   40

df2

    Name  Age
0   John   23
2  Smith   12
3   Wale   34
5    Tom   44
6  Menda   28
8  Yuswa   40
```

The difference between the two would be:
```
df1[~df1.isin(df2)].dropna()

    Name   Age
1   Mike  45.0
4  Marry  27.0
7   Bolt  39.0
```

Where:
- `df1.isin(df2)``df1``df2`- `~``df1``df2`- `.dropna()``NaN`
>  This only works if `len(df1) >= len(df2)`. If `df2` is longer than `df1` you can reverse the expression: `df2[~df2.isin(df1)].dropna()`


------------
    
    
## Answer \#14

 Vote: 1

Created at 2022-04-01 13:17:30

------------

I found the `deepdiff` library is a  tool that also extends well to dataframes if different detail is required or ordering matters. You can experiment with diffing `to_dict('records')`, `to_numpy()`, and other exports:
```
import pandas as pd
from deepdiff import DeepDiff

df1 = pd.DataFrame({
    'Name':
        ['John','Mike','Smith','Wale','Marry','Tom','Menda','Bolt','Yuswa'],
    'Age':
        [23,45,12,34,27,44,28,39,40]
})

df2 = df1[df1.Name.isin(['John','Smith','Wale','Tom','Menda','Yuswa'])]

DeepDiff(df1.to_dict(), df2.to_dict())
# {'dictionary_item_removed': [root['Name'][1], root['Name'][4], root['Name'][7], root['Age'][1], root['Age'][4], root['Age'][7]]}
```



------------
    
    
## Answer \#15

 Vote: 1

Created at 2022-11-24 20:14:30

------------


# Symmetric Difference


If you are interested in the rows that are only in one of the dataframes but not both, you are looking for the set difference:
```
pd.concat([df1,df2]).drop_duplicates(keep=False)
```

> ⚠️ Only works, if both dataframes do not contain any duplicates.

# Set Difference / Relational Algebra Difference


If you are interested in the relational algebra difference / set difference, i.e. `df1-df2` or `df1\df2`:
```
pd.concat([df1,df2,df2]).drop_duplicates(keep=False)
```

> ⚠️ Only works, if both dataframes do not contain any duplicates.


------------
    
    
## Answer \#16

 Vote: 1

Created at 2023-01-26 17:08:29

------------

Another possible solution is to use [numpy broadcasting](https://numpy.org/doc/stable/user/basics.broadcasting.html):
```
df1[np.all(~np.all(df1.values == df2.values[:, None], axis=2), axis=0)]
```

Output:
```
Name  Age
1   Mike   45
4  Marry   27
7   Bolt   39
```



------------
    
    
## Answer \#17

 Vote: 0

Created at 2021-02-10 21:13:00

------------

Using the lambda function you can filter the rows with `_merge` value `“left_only”` to get all the rows in `df1` which are missing from `df2`
```
df3 = df1.merge(df2, how = 'outer' ,indicator=True).loc[lambda x :x['_merge']=='left_only']
df
```



------------
    
    
## Answer \#18

 Vote: 0

Created at 2023-01-20 13:45:25

------------

Try this one:
`df_new = df1.merge(df2, how='outer', indicator=True).query('_merge == "left_only"').drop('_merge', 1)`
It will result a new dataframe with the differences: the values that exist in df1 but not in df2.


------------
    
    