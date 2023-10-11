
# Post \#70374346 [Link](https://stackoverflow.com/questions/70374346/)

## How to remove special characters from rows in pandas dataframe

**Vote**: 6 (473/702) **Views**: 1419 (628/702) 

**Internal ID** \#1-3-218

Created at 2021-12-16 05:44:58

Tags: `python` `pandas` `dataframe`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a column in pandas data frame like the one shown below;
```
LGA

Alpine (S)
Ararat (RC)
Ballarat (C)
Banyule (C)
Bass Coast (S)
Baw Baw (S)
Bayside (C)
Benalla (RC)
Boroondara (C)
```

What I want to do, is to remove all the special characters from the ending of each row. ie. (S), (RC).
Desired output should be;
LGA
```
Alpine
Ararat
Ballarat
Banyule
Bass Coast
Baw Baw
Bayside
Benalla
Boroondara
```

I am not quite sure how to get desired output mentioned above.
Any help would be appreciated.
Thanks


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-12-16 06:33:15

------------

I have different approach using regex. It will delete anything between brackets:
```
import re
import pandas as pd
df = {'LGA': ['Alpine (S)', 'Ararat (RC)', 'Bass Coast (S)']  }
df = pd.DataFrame(df)
df['LGA'] = [re.sub("[\(\[].*?[\)\]]", "", x).strip() for x in df['LGA']] # delete anything between brackets
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-12-16 05:59:11

------------

```
import pandas as pd
df = {'LGA': ['Alpine (S)', 'Ararat (RC)', 'Bass Coast (S)']  }
df = pd.DataFrame(df)
df[['LGA','throw away']] = df['LGA'].str.split('(',expand=True)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-16 07:46:54

------------

You can use `Pandas` str.replace
```
…
dataf['LGA'] = dataf['LGA'].str.replace(r"\([^()]*\)", "", regex=True)
```


### Demo


```
import pandas as pd

dataf = pd.DataFrame({
"LGA":\
"""Alpine (S)
Ararat (RC)
Ballarat (C)
Banyule (C)
Bass Coast (S)
Baw Baw (S)
Bayside (C)
Benalla (RC)
Boroondara (C)""".split("\n")
})

output = dataf['LGA'].str.replace(r"\([^()]*\)", "", regex=True)

print(output)
```

```
0        Alpine 
1        Ararat 
2      Ballarat 
3       Banyule 
4    Bass Coast 
5       Baw Baw 
6       Bayside 
7       Benalla 
8    Boroondara 
Name: LGA, dtype: object
```



------------
    
    