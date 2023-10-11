
# Post \#59827464 [Link](https://stackoverflow.com/questions/59827464/)

## how to fill a Pandas dataframe column with a list containing string values

**Vote**: 3 (575/702) **Views**: 15399 (353/702) 

**Internal ID** \#1-3-197

Created at 2020-01-20 16:32:23

Tags: `python` `python-3.x` `pandas` `numpy`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a dataframe with 142 rows.
I have created a new column. I want to fill this new column with a list containing strings.

```
my_list = ['abc','def','hig']
df['names'] = df['names'].fill(my_list) #pseudo code
```


I want to fill all the 142 rows in 'names' column with string values in the list in the same order. 

```
The output should be as:   

     names
   1 abc
   2 def
   3 hig
   4 abc
   5 def
```



----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2020-01-20 16:41:30

------------

I think you need something like below withh `np.resize` which will resize the list according to the dataframe.

Example dataframe:

```
df = pd.DataFrame(np.random.randint(0,100,size=(5,4)),columns=list('ABCD'))

    A   B   C   D
0  10  99   0  61
1  55   0  52  53
2  34  88  79  54
3  25  32   1  89
4  39  30  77   5
```


Solution:

```
mylist=['abc','def','hig']
df['names'] = np.resize(mylist,len(df))
print(df)
```



---



```
A   B   C   D names
0  10  99   0  61   abc
1  55   0  52  53   def
2  34  88  79  54   hig
3  25  32   1  89   abc
4  39  30  77   5   def
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-01-20 16:39:09

------------

You almost had it.  Try this,

```
import pandas as pd
list = ['abc','def','hig']
df=pd.DataFrame(list)
df.columns=['names'] #provides a name for the column

df_repeated = pd.concat([df]*48, ignore_index=True)
```


which gives you 48*3=144 rows.  Then,

```
df_repeated =df_repeated.reindex(df_repeated.index.drop(144)).reset_index(drop=True)
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-05-09 11:27:10

------------

just assign the list to the column like so; it will simultaneously create the column called 'names' and fill it with the list :
```
df['names'] = my_list
```



------------
    
    