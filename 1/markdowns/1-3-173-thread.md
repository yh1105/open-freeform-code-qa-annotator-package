
# Post \#61810757 [Link](https://stackoverflow.com/questions/61810757/)

## Find total number of days in a year (pandas)

**Vote**: 8 (428/702) **Views**: 8213 (460/702) 

**Internal ID** \#1-3-173

Created at 2020-05-15 02:33:27

Tags: `python-3.x` `pandas`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Is there a way to calculate the total number of days in a year given a datetime format of YYYY-MM-DD in Pandas? I have explored dt.dayofyear but this function seem to provide the number of days from the start of the year until my datetime variable.

As an example, if my date is 2020-03-30, I should know that this is the year of 2020 and there are 366 days (leap year). If it is 2019-03-30, then it should return 365 days (non-leap year).

Tried to search through the pandas dt documentation but can't seem to find any.

Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-05-15 02:39:20

------------

I'm not sure if there's exactly a function to calculate the number of days given the year as input

I would use this for calculating the number of days in any year:

```
year=2020
number_of_days = sum([pd.Period(f'{year}-{i}-1').daysinmonth for i in range(1,13)])
```


but you can also use pd.Period and check for leap years

```
import pandas as pd
p = pd.Period('2020-03-30')
if p.is_leap_year:
   days = 366
else:
   days = 365
print(days)
```


both would OUTPUT for 2020:

```
366
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-05-05 05:43:47

------------

```
import datetime
import calendar


def days_in_year(year=datetime.datetime.now().year):
    return 365 + calendar.isleap(year)
```



------------
    
    
## Answer \#2

 Vote: 7

Created at 2021-07-28 14:19:10

------------

```
year = 2021
pd.Timestamp(year, 12, 31).dayofyear
```



------------
    
    
## Answer \#3

 Vote: 4

Created at 2020-05-15 02:56:06

------------

You can also use the calendar module as follows:

```
import datetime as dt
import calendar
date=dt.datetime.strptime('2020-03-30', '%Y-%m-%d')
year=date.year
days=366 if calendar.isleap(year) else 365
```



------------
    
    