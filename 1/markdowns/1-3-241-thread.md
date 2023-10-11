
# Post \#69954697 [Link](https://stackoverflow.com/questions/69954697/)

## Why does .loc assignment with two sets of brackets result in NaN in a pandas.DataFrame?

**Vote**: 10 (387/702) **Views**: 1394 (631/702) 

**Internal ID** \#1-3-241

Created at 2021-11-13 13:27:32

Tags: `python` `pandas` `dataframe` `nan` `series`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a DataFrame:

|  | name | age | 
|  | ---- | --- | 
| 0 | Paul | 25 | 
| 1 | John | 27 | 
| 2 | Bill | 23 | 


I know that if I enter:
> ```
df[['name']] = df[['age']]
```

I'll get the following:

|  | name | age | 
|  | ---- | --- | 
| 0 | 25 | 25 | 
| 1 | 27 | 27 | 
| 2 | 23 | 23 | 


But I expect the  result from the command:
> ```
df.loc[:, ['name']] = df.loc[:, ['age']]
```

But instead, I get this:

|  | name | age | 
|  | ---- | --- | 
| 0 | NaN | 25 | 
| 1 | NaN | 27 | 
| 2 | NaN | 23 | 


For some reason, if I omit those square brackets `[]` around column names, I'll get exactly what I expected. That is the command:
> ```
df.loc[:, 'name'] = df.loc[:, 'age']
```

gives the right result:

|  | name | age | 
|  | ---- | --- | 
| 0 | 25 | 25 | 
| 1 | 27 | 27 | 
| 2 | 23 | 23 | 


`.loc` Is it some sort of a bug or is it intended behaviour? I can't figure out the reason for such a behaviour.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-11-13 13:46:42

------------

That's because for the `loc` assignment all index axes are aligned, including the columns: Since `age` and `name` do not match, there is no data to assign, hence the NaNs.
You can make it work by renaming the columns:
```
df.loc[:, ["name"]] = df.loc[:, ["age"]].rename(columns={"age": "name"})
```

or by accessing the numpy array:
```
df.loc[:, ["name"]] = df.loc[:, ["age"]].values
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-11-13 14:32:17

------------


## From the Docs Pandas Data Alignment(emphasis mine):


> > pandas  when setting Series and DataFrame from 
You can find this excerpt labelled with Warning under the [Basics header](https://pandas.pydata.org/docs/user_guide/indexing.html#basics).
They have given an example to explain.
```
In [9]: df[['A', 'B']]
Out[9]: 
                   A         B
2000-01-01 -0.282863  0.469112
2000-01-02 -0.173215  1.212112
2000-01-03 -2.104569 -0.861849
2000-01-04 -0.706771  0.721555
2000-01-05  0.567020 -0.424972
2000-01-06  0.113648 -0.673690
2000-01-07  0.577046  0.404705
2000-01-08 -1.157892 -0.370647

In [10]: df.loc[:, ['B', 'A']] = df[['A', 'B']]

In [11]: df[['A', 'B']]
Out[11]: 
                   A         B
2000-01-01 -0.282863  0.469112
2000-01-02 -0.173215  1.212112
2000-01-03 -2.104569 -0.861849
2000-01-04 -0.706771  0.721555
2000-01-05  0.567020 -0.424972
2000-01-06  0.113648 -0.673690
2000-01-07  0.577046  0.404705
2000-01-08 -1.157892 -0.370647
```

:
> > This will  modify df because the column 

### To Explicitly Avoid Automatic Alignment


> Accessing the array can be useful when you need to do some operation without the index (to disable automatic alignment, for example).
The alignment comes into play when LHS and RHS are dataframes. To avoid alignment try using.
```
df.loc[:, ['B', 'A']] = df[['A', 'B']].to_numpy()
```


---


You have two cases at hand,
- `.loc``pd.DataFrame`- `.loc``pd.Series`

## .loc assignment in pd.DataFrame


In `pd.DataFrame` has 2 axes `index` and `columns`. So, when you do
> ```
df.loc[:, ['name']] = df.loc[:, ['age']]
```

LHS has column `A` which doesn't align with RHS column `B` hence resulting in all `NaN` after assignment.
- 
## Data Alignment and arthimetics


From Docs `DataAlignment`(emphasis mine)
> Data alignment between DataFrame objects automatically . Again, the resulting object will have the union of the column and row labels.
You can find this behaviour in most of the pandas' operations if not all. Example, addition, subtraction, multiplication etc. Non matching indices and columns are filled with `NaN`.
Example from Data Alignment and Arthimetics
> ```
df = pd.DataFrame(np.random.randn(10, 4), columns=["A", "B", "C", "D"])
df2 = pd.DataFrame(np.random.randn(7, 3), columns=["A", "B", "C"])

df + df2 

         A         B         C   D
0  0.045691 -0.014138  1.380871 NaN
1 -0.955398 -1.501007  0.037181 NaN
2 -0.662690  1.534833 -0.859691 NaN
3 -2.452949  1.237274 -0.133712 NaN
4  1.414490  1.951676 -2.320422 NaN
5 -0.494922 -1.649727 -1.084601 NaN
6 -1.047551 -0.748572 -0.805479 NaN
7       NaN       NaN       NaN NaN
8       NaN       NaN       NaN NaN
9       NaN       NaN       NaN NaN
```

To answer your [comment](https://stackoverflow.com/questions/69954697/weird-behaviour-of-assignment-in-pandas-dataframe#comment123659697_69954848)
> But why do column indexes need to match? I can see why one want row indexes to match, but why column indexes?
Let's take a look at the above example, if columns are not aligned how would you add two DataFrames? It makes sense to align them on columns and indices.

---



## .loc assignment in pd.Series


`pd.Series` has only  axis i.e `index`. That is the reason why it worked when you did
> ```
df.loc[:, 'name'] = df.loc[:, 'age']
```

As `pd.Series` has only one axis, pandas tried to align `index` and it worked. Of course, if `index` doesn't align it results in `NaN` values.

## From Docs Series Alignment(emphasis mine):


> > The result of an  will have the union of the indexes involved. If a  in one Series or the other, the `NaN`.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-11-13 14:02:09

------------

When you use double brackets [[]] you are assigning a DataFrame. What you want is assign a (column) Series, and for that you use only one bracket [].
Here is some code:
```
import pandas as pd
df = pd.DataFrame({'name':['Paul','John','Bill'], 'age':[25,27,23]})
print('Inital Dataframe:\n',df)

df[['name']] = df[['age']]
print("\ndf[['name']] = df[['age']]\n",df)

print("df.loc[:, ['age']]:", type(df.loc[:, ['age']]))
print("df.loc[:, ['name']]:", type(df.loc[:, ['name']]))
df.loc[:, ['name']] = df.loc[:, ['age']]
print("\ndf.loc[:, ['name']] = df.loc[:, ['age']]\n",df)
    
print('=======================')
df = pd.DataFrame({'name':['Paul','John','Bill'], 'age':[25,27,23]})
print('Inital Dataframe:\n',df)

print("type(df.loc[:, 'age']):", type(df.loc[:, 'age']))
print("type(df.loc[:, 'name']):", type(df.loc[:, 'name']))
df.loc[:, 'name'] = df.loc[:, 'age']
print("\ndf.loc[:, 'name'] = df.loc[:, 'age']\n",df)
```

And the output:
```
Inital Dataframe:
    name  age
0  Paul   25
1  John   27
2  Bill   23

df[['name']] = df[['age']]
    name  age
0    25   25
1    27   27
2    23   23
df.loc[:, ['age']]: <class 'pandas.core.frame.DataFrame'>
df.loc[:, ['name']]: <class 'pandas.core.frame.DataFrame'>

df.loc[:, ['name']] = df.loc[:, ['age']]
    name   age
0   NaN  25.0
1   NaN  27.0
2   NaN  23.0
=======================
Inital Dataframe:
    name  age
0  Paul   25
1  John   27
2  Bill   23
type(df.loc[:, 'age']): <class 'pandas.core.series.Series'>
type(df.loc[:, 'name']): <class 'pandas.core.series.Series'>

df.loc[:, 'name'] = df.loc[:, 'age']
    name  age
0    25   25
1    27   27
2    23   23
```

However, here is another strange behaviour: Assigning the double brackets to difference variables, say `df1` and `df2`, and then `df1 = df2` works!
Here is some more code:
```
df = pd.DataFrame({'name':['Paul','John','Bill'], 'age':[25,27,23]})
print('Inital Dataframe:\n',df)

df1 = df.loc[:, ['name']]
df2 = df.loc[:, ['age']]
print("\ndf1 = df.loc[:, ['name']]\n",df1)
print("\ndf2 = df.loc[:, ['age']]\n",df2)

df1=df2
print("\ndf1=df2\ndf1:\n",df1)
```

And the output:
```
Inital Dataframe:
    name  age
0  Paul   25
1  John   27
2  Bill   23

df1 = df.loc[:, ['name']]
    name
0  Paul
1  John
2  Bill

df2 = df.loc[:, ['age']]
    age
0   25
1   27
2   23

df1=df2
df1:
    age
0   25
1   27
2   23
```



------------
    
    