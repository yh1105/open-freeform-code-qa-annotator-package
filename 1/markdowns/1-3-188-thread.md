
# Post \#59441786 [Link](https://stackoverflow.com/questions/59441786/)

## How to extract a cell value from a Dataframe in Pandas

**Vote**: 14 (336/702) **Views**: 20567 (302/702) 

**Internal ID** \#1-3-188

Created at 2019-12-22 05:17:36

Tags: `python` `pandas` `dataframe`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a dataframe with 3 columns, like this:

[](https://i.stack.imgur.com/6Sluv.jpg)

I want to search for 12295682 in instrument_token column and extract the related tradingsymbol UBL20JANFUT.

How do I do that?

thanks in advance


----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2019-12-22 05:20:37

------------

You can use [boolean indexing](http://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#boolean-indexing) with [DataFrame.loc](http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html) for filter by condition and by column name:

```
s = df.loc[df['instrument_token'].eq(12295682), 'tradingsymbol']
#alternative
s = df.loc[df['instrument_token'] == 12295682, 'tradingsymbol']
```


And then get first value of `Series`:

```
a = s.iat[0]
a = s.iloc[0]
a = s.tolist()[0]
a = s.to_array()[0]
#general solution if not match condition and select first value failed
a = next(iter(s), 'no match')
```


Another idea is use [DataFrame.set_index](http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.set_index.html) fo index by column `instrument_token`:

```
df = df.set_index('instrument_token')
```


Anf then select by [DataFrame.loc](http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html) or 
[DataFrame.at](http://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.at.html):

```
a = df.loc[12295682, 'tradingsymbol']
a = df.at[12295682, 'tradingsymbol']
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2019-12-22 05:26:18

------------

You can use:

```
list(df.loc[df['instrument_token'] == '12295682', 'tradingsymbol'])[0]

# UBL20JANFUT
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2019-12-22 05:20:43

------------

Filter the df based on the specified value then select the desired column.
```
df.loc[df['instrument_token'] == 12295682]['tradingsymbol']
```



------------
    
    
## Answer \#3

 Vote: -1

Created at 2019-12-22 06:09:06

------------

You can try:

```
df2=df['Trading_symbol'][df['instrument_token']==12295682]
```



------------
    
    