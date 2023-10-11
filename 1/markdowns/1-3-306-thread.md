
# Post \#61704346 [Link](https://stackoverflow.com/questions/61704346/)

## in python , How I can save the result to csv , AttributeError: 'dict' object has no attribute 'to_csv'

**Vote**: 1 (672/702) **Views**: 8248 (456/702) 

**Internal ID** \#1-3-306

Created at 2020-05-09 22:01:51

Tags: `python`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

How I can save the result to csv in arranged way ? meaning adding extra columns explain what 'added' and what 'removed' and what 'changed'

I tried diff.to_csv('diff.csv') and got this error

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'dict' object has no attribute 'to_csv'
```


This is the python code

```
from csv_diff import load_csv, compare
diff = compare(
    load_csv(open("list1.csv"), key="ean"),
    load_csv(open("list2.csv"), key="ean")
)
diff.to_csv('diff.csv')
```


 



```
price, oldprice,title,brand,category,unit,ean,,
17,,VR BOX Virtual Reality 3D Glasses Bluetooth Game Remote Control For Phone Iphone,other,3d glasses,2023700513,272434,,
18,,3d Glasses,other,3d glasses,1493500513,2272434,,
22,,Zefas Active 3D GlassesFor SmartPhones,zefas,3d glasses,1342700513,2272456,,
22.98,,3D Glasses Circular Polarized Lenses for Polarized TV| 3D Cinemas,other,3d glasses,1992100513,2272483,,
25,,max3 pro,other,3d glasses,1904600513,2272432,,
28.27,,Red Blue Clip on Anaglyph Glasses,other,3d glasses,2068900513,2272453,,
```




```
price,oldprice,title,brand,category,unit,ean,,
22.98,,3D Glasses Circular Polarized Lenses for Polarized TV| 3D Cinemas,other,3d  glasses,1992100513,2272483,,
25,,max3 pro,other,3d glasses,1904600513,2272432,,
59,,Red-blue Cyan Anaglyph 3D,terratec,3d glasses,2103700513,2272428,,
65,,Sinogoodies Passive 3D GlassesFor SmartPhones,sinogoodies,3d glasses,1603700513,2272464,,
69.91,,G15-DLP 3D Active Shutter Glasses for DLP-LINK DLP LINK 3D for Optoma Sharp LG Acer BenQ Projectors,other,3d glasses,2039200513,227243,,
70,,Sinogoodies Passive 3D GlassesFor Multi,sinogoodies,3d glasses,1603600513,2272464,,
```


 

```
compare(
        load_csv(open("list1.csv"), key="ean"),
        load_csv(open("list2.csv"), key="ean")
    )
```


is 

```
{'added': [{'price': '59', 'oldprice': '', 'title': 'Red-blue Cyan Anaglyph 3D',
 'brand': 'terratec', 'category': '3d glasses', 'unit': '2103700513', 'ean': '22
72428', '': ''}, {'price': '70', 'oldprice': '', 'title': 'Sinogoodies Passive 3
D GlassesFor Multi', 'brand': 'sinogoodies', 'category': '3d glasses', 'unit': '
1603600513', 'ean': '2272464', '': ''}, {'price': '69.91', 'oldprice': '', 'titl
e': 'G15-DLP 3D Active Shutter Glasses for DLP-LINK DLP LINK 3D for Optoma Sharp
 LG Acer BenQ Projectors', 'brand': 'other', 'category': '3d glasses', 'unit': '
2039200513', 'ean': '227243', '': ''}], 'removed': [{'price': '17', 'oldprice':
'', 'title': 'VR BOX Virtual Reality 3D Glasses Bluetooth Game Remote Control Fo
r Phone Iphone', 'brand': 'other', 'category': '3d glasses', 'unit': '2023700513
', 'ean': '272434', '': ''}, {'price': '18', 'oldprice': '', 'title': '3d Glasse
s', 'brand': 'other', 'category': '3d glasses', 'unit': '1493500513', 'ean': '22
72434', '': ''}, {'price': '22', 'oldprice': '', 'title': 'Zefas Active 3D Glass
esFor SmartPhones', 'brand': 'zefas', 'category': '3d glasses', 'unit': '1342700
513', 'ean': '2272456', '': ''}, {'price': '28.27', 'oldprice': '', 'title': 'Re
d Blue Clip on Anaglyph Glasses', 'brand': 'other', 'category': '3d glasses', 'u
nit': '2068900513', 'ean': '2272453', '': ''}], 'changed': [], 'columns_added':
[], 'columns_removed': []}
```




```
import pandas as pd
dataframe = pd.DataFrame(diff)
dataframe.to_csv("data.csv", header=True)
```



----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2020-05-09 22:53:18

------------

The other solutions are not working because your data does not have the correct structure for a `DataFrame`. You need to access the lists of dicts from the top level keys first.

```
import pandas

df1  = pandas.DataFrame(diff['added'])
df1['change'] = 'added'
df2 = pandas.DataFrame(diff['removed'])
df2['change'] = 'removed'

df = df1.append(df2)
df.to_csv('diff.csv')
```



# Change per Comment Request



```
df1 = pandas.read_csv('list1.csv')
df1['version'] = 'list1'
df2 = pandas.read_csv('list2.csv')
df2['version'] = 'list2'

# keep only columns 'version', 'ean', 'price'
diff = df1.append(df2)[['version', 'ean', 'price']]
# keep only duplicated eans, which will only occur
# for eans in both original lists
diff = diff[diff['ean'].duplicated(keep=False)]
# perform a pivot https://pandas.pydata.org/pandas-docs/stable/user_guide/reshaping.html
diff = diff.pivot(index='ean', columns='version', values='price')

# back to a normal dataframe
diff = diff.reset_index()
diff.columns.name = None

# rename columns and keep only what we want
diff = diff.rename(columns={'list1': 'price1', 'list2': 'price2'})[['ean', 'price1', 'price2']]
diff['difference'] = diff['price2'] - diff['price1']
```


[](https://i.stack.imgur.com/ZlDqi.png)


------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-05-09 22:08:29

------------

you can use `to_csv` method that's being provided by `pandas` :

```
import pandas as pd 

df = pd.DataFrame(dict([ (k,pd.Series(v)) for k,v in diff.items() ]))
df.to_csv('diff.csv')
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-05-09 22:11:32

------------

you can use pandas package 
pip install pandas

```
import pandas as pd
added_frame = pd.DataFrame(yourdict['added'])
removed_frame = pd.DataFrame(yourdict['removed'])
df = added_frame.append(removed_frame)
df.to_csv("data.csv", header=True)
```


[Pandas Docs](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_csv.html)


------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-05-09 22:20:01

------------

The error `'dict' object has no attribute 'to_csv'` means that your `diff` variable is a dictionary and therefore has not method called `.to_csv`. 

You could try to coerce your `diff` into a csv file but it will not work properly. Your `diff` is currently an dictionary with values that are . This format will not work for csv. If you use the `Pandas` package you need your dictionary to have values that are arrays of . Here is the [docs](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_csv.html) with an example.

I recommend you think about how to format your `diff` first and then convert to csv.

One option to make this work is to create two csvs from the `diff` dictionary. The added and removed values are actually in the right format for csv. Here is an example:

```
import csv

keys = diff['added'][0].keys()
with open('file_name.csv', 'w') as output_file:
  dict_writer = csv.DictWriter(output_file, keys)
  dict_writer.writeheader()
  dict_writer.writerows(diff['added'])
```


These are the [docs](https://docs.python.org/3/library/csv.html#csv.DictWriter) for the csv package.

EDIT: keys should get the keys from the first element in added.


------------
    
    