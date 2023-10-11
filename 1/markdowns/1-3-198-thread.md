
# Post \#56611698 [Link](https://stackoverflow.com/questions/56611698/)

## Pandas: How to read CSV file from google drive public?

**Vote**: 23 (239/702) **Views**: 53568 (156/702) 

**Internal ID** \#1-3-198

Created at 2019-06-15 15:24:44

Tags: `python` `pandas`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I searched similar questions about reading csv from URL but I could not find a way to read csv file from google drive csv file.

My attempt:

```
import pandas as pd

url = 'https://drive.google.com/file/d/0B6GhBwm5vaB2ekdlZW5WZnppb28/view?usp=sharing'
dfs = pd.read_html(url)
```


How can we read this file in pandas?

Related links:  

- [Pandas read_csv from url](https://stackoverflow.com/questions/32400867/pandas-read-csv-from-url)- [https://pandas.pydata.org/pandas-docs/version/0.22/io.html#io-read-html](https://pandas.pydata.org/pandas-docs/version/0.22/io.html#io-read-html)


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2021-04-26 16:22:25

------------


# Using pandas


```
import pandas as pd

url='https://drive.google.com/file/d/0B6GhBwm5vaB2ekdlZW5WZnppb28/view?usp=sharing'
file_id=url.split('/')[-2]
dwn_url='https://drive.google.com/uc?id=' + file_id
df = pd.read_csv(dwn_url)
print(df.head())
```


# Using pandas and requests


```
import pandas as pd
import requests
from io import StringIO

url='https://drive.google.com/file/d/0B6GhBwm5vaB2ekdlZW5WZnppb28/view?usp=sharing'

file_id = url.split('/')[-2]
dwn_url='https://drive.google.com/uc?export=download&id=' + file_id
url2 = requests.get(dwn_url).text
csv_raw = StringIO(url2)
df = pd.read_csv(csv_raw)
print(df.head())
```


# output


```
sex   age state  cheq_balance  savings_balance  credit_score  special_offer
0  Female  10.0    FL       7342.26          5482.87           774           True
1  Female  14.0    CA        870.39         11823.74           770           True
2    Male   0.0    TX       3282.34          8564.79           605           True
3  Female  37.0    TX       4645.99         12826.76           608           True
4    Male   NaN    FL           NaN          3493.08           551          False
```



------------
    
    
## Answer \#1

 Vote: 36

Created at 2019-06-15 16:05:04

------------

This worked for me
```
import pandas as pd
url='https://drive.google.com/file/d/0B6GhBwm5vaB2ekdlZW5WZnppb28/view?usp=sharing'
url='https://drive.google.com/uc?id=' + url.split('/')[-2]
df = pd.read_csv(url)
```



------------
    
    
## Answer \#2

 Vote: 18

Created at 2020-06-26 10:18:11

------------

To read CSV file from google drive you can do that.
```
import pandas as pd

url = 'https://drive.google.com/file/d/0B6GhBwm5vaB2ekdlZW5WZnppb28/view?usp=sharing'
path = 'https://drive.google.com/uc?export=download&id='+url.split('/')[-2]
df = pd.read_csv(path)
```

I think this is the easiest way to read CSV files from google drive.
hope your "Anyone with the link" option enables in google drive.


------------
    
    
## Answer \#3

 Vote: 5

Created at 2019-06-15 15:37:19

------------

I would recommend you using the following code:

```
import pandas as pd
import requests
from io import StringIO

url = requests.get('https://doc-0g-78-docs.googleusercontent.com/docs/securesc/ha0ro937gcuc7l7deffksulhg5h7mbp1/5otus4mg51j69f99n47jgs0t374r46u3/1560607200000/09837260612050622056/*/0B6GhBwm5vaB2ekdlZW5WZnppb28?e=download')
csv_raw = StringIO(url.text)
dfs = pd.read_csv(csv_raw)
```


hope this helps


------------
    
    
## Answer \#4

 Vote: 4

Created at 2021-04-26 09:54:46

------------

Simply change de URL from Google Drive using `uc?id=`, and then pass it to the `read_csv` function. In this example:
```
url = 'https://drive.google.com/uc?id=0B6GhBwm5vaB2ekdlZW5WZnppb28'
dfs = pd.read_csv(url)
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2022-01-10 15:05:04

------------

The other answers are great for reading a publicly accessible file but, if trying to read a private file that has been shared with an email account, you may want to consider using [PyDrive](https://pythonhosted.org/PyDrive/).
There are many ways to authenticate ([OAuth](https://pythonhosted.org/PyDrive/oauth.html), [using a GCP service account](https://stackoverflow.com/questions/60736955/how-to-connect-pydrive-with-an-service-account), etc). Once authenticated, reading a CSV can be as simple as getting the file ID and fetching its contents:
```
from io import StringIO

from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive

# Assuming authentication has been performed and stored in a variable called gauth
drive = GoogleDrive(gauth)
params = {
    'q': f"id='{file_id}' = id and mimeType='text/csv'"
}
# List all files that satisfy the query
file_list = drive.ListFile(params).GetList()

gdrive_csv_file = file_list[0]
input_csv = StringIO(gdrive_csv_file.GetContentString())
    
df = pd.read_csv(input_csv)
```



------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-05-03 13:02:51

------------

Here is similar implementation using R
```
library(tidyverse)

url='https://drive.google.com/file/d/0B6GhBwm5vaB2ekdlZW5WZnppb28/view?usp=sharing'
file_id=nth(strsplit(url, split = "/")[[1]], -2)
dwn_url=paste('https://drive.google.com/uc?id=',file_id,sep = "")
df = read_csv(dwn_url)

head(df)
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2021-09-24 11:15:36

------------

In case you're using Google Colab you can add file to your Drive and type (default folder names):
```
df = pd.read_csv('/content/drive/MyDrive/.../your_file.csv')
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2021-10-03 06:44:18

------------

If you are using google colab as notebook you can directly mount the drive and then copy the path of file:
```
df = pd.read_csv('/content/drive/MyDrive/Dataset/dataset.csv')
    df.head()
```



------------
    
    
## Answer \#9

 Vote: 0

Created at 2023-02-08 07:05:37

------------

Google has updated the part of the query string URL now (usp=share_link).
The following code works now:
```
import pandas as pd
url="https://drive.google.com/file/d/1a7qwzU2mbaJPkFQZMJCkdE37Ne2DbgHA/view?usp=share_link"
reconstructed_url='https://drive.google.com/uc?id=' + url.split('/')[-2]
df = pd.read_csv(reconstructed_url)
df
```



------------
    
    