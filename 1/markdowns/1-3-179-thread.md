
# Post \#71591106 [Link](https://stackoverflow.com/questions/71591106/)

## Groupby and filter by max value in pandas

**Vote**: 3 (575/702) **Views**: 2860 (574/702) 

**Internal ID** \#1-3-179

Created at 2022-03-23 16:55:30

Tags: `r` `pandas` `dataframe` `filter`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python` `r`)

----------

**Notepad**


----------

I am working on a dataframe that looks like the following: (ID 2 added in edit)
```
+-------+----+------+------+
| Value | ID | Date | ID 2 |
+-------+----+------+------+
|     1 |  5 | 2012 | 111 |
|     1 |  5 | 2013 | 112 |
|     0 | 12 | 2017 | 113 |
|     0 | 12 | 2022 | 114 |
|     1 | 27 | 2005 | 115 |
|     1 | 27 | 2011 | 116 |
+-------+----+------+-----+
```

Only using rows with "Value" == "1" ("value is boolean), I would like to group the dataframe by ID and input the string "latest" to new (blank) column, giving the following output:
```
+-------+----+------+--------+
| Value | ID | Date | Latest |
+-------+----+------+--------+
|     1 |  5 | 2012 |        |
|     1 |  5 | 2013 | Latest |
|     0 | 12 | 2017 |        |
|     0 | 12 | 2022 |        |
|     1 | 27 | 2005 |        |
|     1 | 27 | 2011 | Latest |
+-------+----+------+--------+
```

The syntax of pandas is throwing me off as I am fairly new to Python.
In R I suppose I would be trying something like
```
df %>% select(Value == "1") %>% group_by(ID) %>% select(max(Date)
```

but I am not sure of the syntax in Pandas...I am trying to first select the subset of rows that meets the criteria "value == 1" by using
```
q = df.query('Value == 1')
my_query_index = q.index
my_query_index
```

This returns the index of all the rows but I am not sure how to incorporate this into the dataframe before grouping and filtering by max(date).
All help appreciated. Thank you.
EDIT: I used the pinned answer as following:
```
latest = df.query('Value==1').groupby("ID").max("Year").assign(Latest = "Latest")
df = pd.merge(df,latest,how="outer")
df
```

But I have since realized some of the max years are the same, i.e. there could be 4 rows, all with max year 2017. For the tiebreaker, I need to use the max ID 2 within groups. I have added `.groupby("ID 2").max("ID 2")` to the line of code, giving
```
latest = df.query('Value==1').groupby("ID").max("Year").groupby("ID 2").max("ID 2").assign(Latest = "Latest")
df = pd.merge(df,latest,how="outer")
df
```

but it is giving me a dataframe completely different than the one desired.
Thank you for your help, all is appreciated.


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-03-23 17:10:01

------------

You can do this:
```
latest = df.query('Value==1').groupby("ID").max("year").assign(Latest = "Latest")
pd.merge(df,latest,how="outer")

   Value  ID  Date  Latest
0      1   5  2012     NaN
1      1   5  2013  Latest
2      0  12  2017     NaN
3      0  12  2022     NaN
4      1  27  2005     NaN
5      1  27  2011  Latest
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-03-23 17:38:54

------------


1. Sort by 'ID' then by 'Date'
2. Use duplicated(keep='last') to identify the last item in each group
3. loc to assign in the right spot



---


```
df = df.sort_values(['ID', 'Date'])
mask1 = df.Value.eq(1)
mask2 = ~df.ID.duplicated(keep='last')

df.loc[mask1 & mask2, 'Latest'] = 'Latest'

df

   Value  ID  Date  Latest
0      1   5  2012     NaN
1      1   5  2013  Latest
2      0  12  2017     NaN
3      0  12  2022     NaN
4      1  27  2005     NaN
5      1  27  2011  Latest
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-03-23 20:23:56

------------

One option is to groupby, using `transform` to get the max, then use a conditional statement with np.where to get the output:
```
max_values = df.groupby("ID").Date.transform("max")
df.assign(Latest=np.where(df.Date.eq(max_values) & df.Value.eq(1), "Latest", ""))

   Value  ID  Date  Latest
0      1   5  2012        
1      1   5  2013  Latest
2      0  12  2017        
3      0  12  2022        
4      1  27  2005        
5      1  27  2011  Latest
```



------------
    
    