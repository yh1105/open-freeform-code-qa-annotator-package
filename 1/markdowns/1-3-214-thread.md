
# Post \#60839909 [Link](https://stackoverflow.com/questions/60839909/)

## Errors running Pandas Profile Report

**Vote**: 12 (356/702) **Views**: 21153 (292/702) 

**Internal ID** \#1-3-214

Created at 2020-03-24 21:52:29

Tags: `python` `pandas` `pandas-profiling`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm trying to run a Profile Report for EDA in conda Jupyter NB, but keep getting errors.
Here is my code thus far:

```
import pandas_profiling
    from pandas_profiling import ProfileReport

    profile = ProfileReport(data)
```


and 

```
profile = pandas_profiling.ProfileReport(data)
```


both of which produce:    

> TypeError: concat() got an unexpected keyword argument 'join_axes'  

Research recommended upgrading to Pandas 1.0, which I'm using. 

Also tried

```
data.profile_report()
```


> AttributeError: 'DataFrame' object has no attribute 'profile_report'  

Any tips on where I am going wrong?  

Addendum...So I finally figured it out. Needed to install latest version of pandas-profiling in conda, which was 202003 version. Too easy.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-04-25 22:41:24

------------

Installed most recent version (March 2020) of pandas-profiling in conda. 

```
conda install -c conda-forge/label/cf202003 pandas-profiling
```


Was then able to `import pandas_profiling` in jupyter notebook


------------
    
    
## Answer \#1

 Vote: 11

Created at 2020-07-20 20:34:48

------------

First I tried just running the command !pip install pandas-profiling  but still was not working but then I found this 
command, now it is working for me.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-04-24 14:34:49

------------

This works with the latest release of pandas profiling as well.

Release date - 14th April, 2020.

Find the link to install the same.

[https://pypi.org/project/pandas-profiling/](https://pypi.org/project/pandas-profiling/)


------------
    
    
## Answer \#3

 Vote: 2

Created at 2020-08-30 10:39:41

------------

Also running into the same issue. I upgraded using
`pip3 install pandas_profiling --upgrade`


------------
    
    
## Answer \#4

 Vote: 2

Created at 2021-04-16 08:10:36

------------

#update Profiling to latest Version first
```
import sys
!{sys.executable} -m pip install -U pandas-profiling[notebook]
!jupyter nbextension enable --py widgetsnbextension

import pandas as pd
from pandas_profiling import ProfileReport

train = pd.read_csv("train.csv")
```

#Generate the profile report
```
report = ProfileReport(train)

print(repr(report.report))

report.to_widgets()
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2021-05-23 10:30:10

------------

I think the problem is  generally with the previous versions of Pandas-Profiling, try upgrading it to a newer version
If you are using google collab
`!pip3 install pandas_profiling --upgrade`
Otherwise use
`$ pip3 install pandas_profiling --upgrade`
After executing this  restart runtime


------------
    
    
## Answer \#6

 Vote: -2

Created at 2021-02-11 04:50:04

------------

Execute below command and once completed restart the runtime
```
!pip install pandas-profiling==2.7.1
```



------------
    
    