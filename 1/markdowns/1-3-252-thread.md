
# Post \#54470917 [Link](https://stackoverflow.com/questions/54470917/)

## Pandas GroupBy and select rows with the minimum value in a specific column

**Vote**: 58 (113/702) **Views**: 40685 (192/702) 

**Internal ID** \#1-3-252

Created at 2019-01-31 23:45:59

Tags: `python` `pandas` `group-by`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a DataFrame with columns A, B, and C. For each value of A, I would like to select the row with the minimum value in column B.
That is, from this:
```
df = pd.DataFrame({'A': [1, 1, 1, 2, 2, 2],
                   'B': [4, 5, 2, 7, 4, 6],
                   'C': [3, 4, 10, 2, 4, 6]})      
    A   B   C
0   1   4   3
1   1   5   4
2   1   2   10
3   2   7   2
4   2   4   4
5   2   6   6
```

I would like to get:
```
A   B   C
0   1   2   10
1   2   4   4
```

For the moment I am grouping by column A, then creating a value that indicates to me the rows I will keep:
```
a = data.groupby('A').min()
a['A'] = a.index
to_keep = [str(x[0]) + str(x[1]) for x in a[['A', 'B']].values]
data['id'] = data['A'].astype(str) + data['B'].astype('str')
data[data['id'].isin(to_keep)]
```

I am sure that there is a much more straightforward way to do this.
I have seen many answers here that use MultiIndex, which I would prefer to avoid.
Thank you for your help.


----------
        
## Answer \#0

**Accepted** Vote: 73

Created at 2019-02-01 00:03:59

------------

I feel like you're overthinking this. Just use `groupby` and `idxmin`:

```
df.loc[df.groupby('A').B.idxmin()]

   A  B   C
2  1  2  10
4  2  4   4
```



---



```
df.loc[df.groupby('A').B.idxmin()].reset_index(drop=True)

   A  B   C
0  1  2  10
1  2  4   4
```



------------
    
    
## Answer \#1

 Vote: 12

Created at 2019-12-15 10:50:13

------------

Had a similar situation but with a more complex column heading  in which case this is needed: 

```
df.loc[df.groupby('A')['B val'].idxmin()]
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-02-15 16:36:09

------------

The accepted answer (suggesting `idxmin`) cannot be used with the pipe pattern. A pipe-friendly alternative is to first sort values and then use `groupby` with `DataFrame.head`:
```
data.sort_values('B').groupby('A').apply(DataFrame.head, n=1)
```

This is possible because by default `groupby` , which is stable and documented behaviour (see [pandas.DataFrame.groupby](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.groupby.html)).
This approach has additional benefits:
- - `.sort_values()````
data.sort_values(['final_score', 'midterm_score']).groupby('year').apply(DataFrame.head, n=1)
```

As with other answers, to exactly match the result desired in the question `.reset_index(drop=True)` is needed, making the final snippet:
```
df.sort_values('B').groupby('A').apply(DataFrame.head, n=1).reset_index(drop=True)
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-09-23 23:25:51

------------

I found an answer a little bit more wordy, but :
This is the example dataset:
```
data = pd.DataFrame({'A': [1,1,1,2,2,2], 'B':[4,5,2,7,4,6], 'C':[3,4,10,2,4,6]})
data

Out:
   A  B   C
0  1  4   3
1  1  5   4
2  1  2  10
3  2  7   2
4  2  4   4
5  2  6   6
```

First we will get the min values on a Series from a groupby operation:
```
min_value = data.groupby('A').B.min()
min_value

Out:
A
1    2
2    4
Name: B, dtype: int64
```

Then, we merge this series result on the original data frame
```
data = data.merge(min_value, on='A',suffixes=('', '_min'))
data

Out:
   A  B   C  B_min
0  1  4   3      2
1  1  5   4      2
2  1  2  10      2
3  2  7   2      4
4  2  4   4      4
5  2  6   6      4
```

Finally, we get only the lines where B is equal to B_min and drop B_min since we don't need it anymore.
```
data = data[data.B==data.B_min].drop('B_min', axis=1)
data

Out:
   A  B   C
2  1  2  10
4  2  4   4
```

I have tested it on very large datasets and this was the only way I could make it work in a reasonable time.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2022-10-03 20:01:08

------------

You can [sort_values](http://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html#pandas.DataFrame.sort_values) and [drop_duplicates](http://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop_duplicates.html#pandas.DataFrame.drop_duplicates):
```
df.sort_values('B').drop_duplicates('A')
```

Output:
```
A  B   C
2  1  2  10
4  2  4   4
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-08-15 01:18:33

------------

The solution is, as written before ;
```
df.loc[df.groupby('A')['B'].idxmin()]
```

If the solution but then if you get an error;
```
"Passing list-likes to .loc or [] with any missing labels is no longer supported.
The following labels were missing: Float64Index([nan], dtype='float64').
See https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#deprecate-loc-reindex-listlike"
```

In my case, there were 'NaN' values at column B. So, I used 'dropna()' then it worked.
```
df.loc[df.groupby('A')['B'].idxmin().dropna()]
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-08-17 05:07:00

------------

You can also boolean indexing the rows where `B` column is minimal value
```
out = df[df['B'] == df.groupby('A')['B'].transform('min')]
```

```
print(out)

   A  B   C
2  1  2  10
4  2  4   4
```



------------
    
    