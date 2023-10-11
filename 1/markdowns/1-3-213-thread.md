
# Post \#61802080 [Link](https://stackoverflow.com/questions/61802080/)

## ExcelWriter ValueError: Excel does not support datetime with timezone when saving df to Excel

**Vote**: 26 (219/702) **Views**: 45754 (178/702) 

**Internal ID** \#1-3-213

Created at 2020-05-14 16:08:42

Tags: `python` `pandas` `xlsxwriter`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm running on this issue for quite a while now.

I set the writer as follows:

```
writer = pd.ExcelWriter(arquivo+'.xlsx', engine = 'xlsxwriter', options = {'remove_timezone': True})
df.to_excel(writer, header = True, index = True)
```


This code is inside s function. The problem is every time I run the code, it gets information from the database, which contains two columns datetime64[ns, UTC] object with time zone info. But when the code to save to Excel runs I receive:

```
ValueError: Excel does not support datetimes with timezones. Please ensure that datetimes are timezone unaware before writing to Excel.
```


I have already tried several things like 'dt.tz_convert', replace(tzinfo=None) and other solutions I have found here and around.

The code runs without problem in my personal computer, my colleague at work with the same machine specs can run the code. Only in my machine it doesn't. I already reinstalled python and all the packages, including formatting the machine and nothing, the error persists.

xlrd v1.1.0

xlsxwriter v1.0.4

python 3.7.4

pandas v0.25.1

If someone could bring some light into this issue I would much appreciate it.

Thanks


----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2020-08-03 22:56:11

------------

What format is your timestamps in?
I just had a similar problem.
I was trying to save a data frame to Excel. However I was getting:
[](https://i.stack.imgur.com/RK3dl.png)
I checked my date format which was in this format `'2019-09-01T00:00:00.000Z'`
This is a timestamp `pandas._libs.tslibs.timestamps.Timestamp`
from `pandas.to_datetime`
which includes a method `date()` that converted the date into a format `"%Y-%m-%d"` that was acceptable by excel
So my code was something like:
```
#Pseudo
df['date'] = old_dates
df['date'] = df['date'].apply(lambda a: pd.to_datetime(a).date()) 
# .date() removes timezone

...df.to_excel etc.
```



------------
    
    
## Answer \#1

 Vote: 21

Created at 2020-12-08 18:39:05

------------

This should do the job, remove timezone from columns before exporting to excel (using tz_localize(None)).
```
# Check which columns have timezones datetime64[ns, UTC] 
df.dtypes

# Remove timezone from columns
df['date'] = df['date'].dt.tz_localize(None)

# Export to excel
df.to_excel('filename.xlsx')
```



------------
    
    
## Answer \#2

 Vote: 14

Created at 2021-03-18 21:40:14

------------

I  found this way easier and more dynamic. This solution you select the columns by the type and applied the desire conversion.
```
date_columns = df.select_dtypes(include=['datetime64[ns, UTC]']).columns
for date_column in date_columns:
    df[date_column] = df[date_column].dt.date
    
df.to_excel('anbima_feed.xlsx',engine='xlsxwriter')
```



------------
    
    
## Answer \#3

 Vote: 4

Created at 2021-05-19 08:36:44

------------

The Accepted Answer works only if you need the Date without the Time in the corresponding Timezone.
If your Time is in UTC as a Epoch you need to convert it to Striftime and then again to Datetime to conserve the Time in the Timezone.
Reference: [https://python-forum.io/thread-31300.html](https://python-forum.io/thread-31300.html)
Example:
the field ts is a timestamp in UTC as Epoch in millisecond.
```
df['ts']
OUT:
0      1619801902867
1      1619765681594
2      1619712291984
3      1619680298648
4      1619629032109
5      1619593388626
6      1619531314509
7      1619509338368
8      1619449287828
9      1619433411243
10     1619103667781
11     1619078244871
12     1619021782951
13     1618990214111
14     1618931135540
15     1618903774632
```

Then you need to convert it into the desired Timezone:
```
df['ts'] = pd.to_datetime(df['ts'],unit='ms').dt.tz_localize('utc').dt.tz_convert('Europe/Vatican')
df['ts'] = df['ts'].apply(lambda a: datetime.datetime.strftime(a,"%Y-%m-%d %H:%M:%S"))
df['ts'] = pd.to_datetime(df['ts'])
```

The Result will look like:
```
df['ts']
OUT:
0     2021-04-30 18:58:22
1     2021-04-30 08:54:41
2     2021-04-29 18:04:51
3     2021-04-29 09:11:38
4     2021-04-28 18:57:12
5     2021-04-28 09:03:08
6     2021-04-27 15:48:34
7     2021-04-27 09:42:18
8     2021-04-26 17:01:27
9     2021-04-26 12:36:51
10    2021-04-22 17:01:07
11    2021-04-22 09:57:24
12    2021-04-21 18:16:22
13    2021-04-21 09:30:14
14    2021-04-20 17:05:35
15    2021-04-20 09:29:34
```

After this, the xlsxwriter will accept it and write the excel without the error Message.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-01-19 21:30:48

------------

There is also another way to use `UTC` parameter in 
```
import pandas as pd
# Adjust time zone from columns
df['date'] = pd.to_datetime( df['date'], errors='coerce',utc=True)
# Export to excel
df.to_excel('filename.xlsx')
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2023-02-23 23:08:13

------------

You can clean your `df.index` rows using:
```
index_renaming = {}
for idx in df.index:
    for x in idx:
        if getattr(x, "tz", None) is not None:
            index_renaming[x] = x.tz_localize(None)
if index_renaming:
    df.rename(index=index_renaming, inplace=True)
```

All other answers are about cleaning `df.values`, which can be done using `applymap`:
```
df.applymap(lambda x: x if getattr(x, "tz", None) is None else x.tz_localize(None))
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-02-14 14:07:55

------------

I encountered the same issue and did some documentation search and found a solution for pandas
The below change (added- `options={'remove_timezone': True}`) worked for me.
```
exwriter = pd.ExcelWriter(fullpath, engine='xlsxwriter', options={'remove_timezone': True})
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2022-06-03 14:41:36

------------

If you are oke with the values in the sheet being a string, you can use the following code to transform the datetime
```
date_columns = df.select_dtypes(include=['datetime64[ns, UTC]']).columns
for date_column in date_columns:
    df[date_column] = df[date_column].apply(str)
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2022-07-15 14:40:08

------------

I had a similar problem. In my case the dates were the index. In case anyone else encounters that very problem (oftentimes with stock/currency/crypto price data) you can use the following:
```
df.index = df.index.tz_localize(None)
df.to_excel(path)
```



------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-12-30 06:54:36

------------

Simply, convert the column to `str`:
```
df['date'] = df['date'].astype(str)
```



------------
    
    