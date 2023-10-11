
# Post \#73257386 [Link](https://stackoverflow.com/questions/73257386/)

## pandas groupby column that has specific value

**Vote**: 2 (631/702) **Views**: 536 (695/702) 

**Internal ID** \#1-3-180

Created at 2022-08-06 05:25:26

Tags: `python` `pandas`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

hi im working with dataset in pandas.
lets say the dataset having
ID,
TEST_TYPE,
TEST_STATUS,
TEST_DATE,
etc
i need to group a kind of column so first i try
```
data_useless[['TEST_TYPE', 'TEST_STATUS']].groupby('TEST_STATUS').count_values()
```

and it worked : showing the result of grouped data by test_status(FAILED TEST and PASS TEST) and count value of that data on dataset
now i want to know and see the data more from the PASS TEST
so i tried
```
data_useless.groupby(['TEST_STATUS'] == 'PASS TEST')
```

and it not working.. showing error, it say 
i need to do something like
in sql :
```
SELECT *
FROM data_useless
WHERE TEST_STATUS = "PASS TEST"
group by TEST_STATUS;
```

please help,,,


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-08-06 05:45:21

------------

You can filter using:
```
data_useless.loc[data_useless['TEST_STATUS'] == 'PASS TEST']
```

Or:
```
data_useless.query('TEST_STATUS == "PASS TEST"')
```

Then, if needed, compute the groupby + aggregation:
```
(data_useless.loc[data_useless['TEST_STATUS'] == 'PASS TEST']
             .groupby(...).agg(...) # or any other groupby function
)
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-08-06 05:40:22

------------

First, why do you need to group by column when you filter it with just one item??
but if you want to do this, you should first filter the data frame and then a group by operation on the result data frame
```
data_useless[data_useless.TEST_STATUS == "PASS TEST"].groupby("TEST_STATUS")
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-08-06 05:43:19

------------

try this
```
data_useless[data_useless['TEST_STATUS']=='PASS TEST'].groupby('TEST_STATUS').count()
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-08-06 05:56:52

------------

If you first make a groupby object, you can pull specific groups using the `get_group()` method:
```
groups = df.groupby('TEST_STATUS')

pass_data = groups.get_group('PASS TEST')

# Now you can do whatever you want with that group:
pass_data.groupby(...).agg(...)
```



------------
    
    