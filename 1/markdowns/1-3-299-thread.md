
# Post \#61852402 [Link](https://stackoverflow.com/questions/61852402/)

## How can I plot a simple plot with seaborn from a python dictionary?

**Vote**: 8 (428/702) **Views**: 16804 (335/702) 

**Internal ID** \#1-3-299

Created at 2020-05-17 13:16:27

Tags: `python` `matplotlib` `seaborn`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a dictionary like this:

```
my_dict = {'Southampton': '33.7%', 'Cherbourg': '55.36%', 'Queenstown': '38.96%'}
```


How can I have a simple plot with 3 bars showing the values of each key in a dictionary?

I've tried:

```
sns.barplot(x=my_dict.keys(), y = int(my_dict.values()))
```


But I get :

> TypeError: int() argument must be a string, a bytes-like object or a number, not 'dict_values'


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2020-05-17 13:29:23

------------

There are several issues in your code:

1. You are trying to convert each value (eg "xx.xx%") into a number. my_dict.values() returns all values as a dict_values object. int(my_dict.values())) means converting the set of all values to a single integer, not converting each of the values to an integer. The former, naturally, makes no sense.
2. Python can't interpret something like "12.34%" as an integer or a float. You need to remove the percentage sign, ie "float(12.34%"[:-1]).
3. Dictionaries are not ordered. Therefore, my_dict.keys() and my_dict.values() are not guaranteed to return keys and values in the key-value pairs in the same order, for example, the keys you get may be ['Southampton', 'Cherbourg', 'Queenstown'] and the values you get may be "55.36%", "33.7", "38.96%". This is no longer a problem in Python >= 3.7 and CPython 3.6; see @AmphotericLewisAcid's comment below.


With all these issues fixed:
```
keys = list(my_dict.keys())
# get values in the same order as keys, and parse percentage values
vals = [float(my_dict[k][:-1]) for k in keys]
sns.barplot(x=keys, y=vals)
```

You get:
[](https://i.stack.imgur.com/d5rXY.png)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-05-17 13:28:48

------------

You need to convert the values to numeric, right now they are strings:

```
import seaborn as sns
my_dict = {'Southampton': '33.7%', 'Cherbourg': '55.36%', 'Queenstown': '38.96%'}
perc =  [float(i[:-1]) for i in my_dict.values()]
sns.barplot(x=list(my_dict.keys()),y=perc)
```


[](https://i.stack.imgur.com/x5vAN.png)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-05-17 13:33:08

------------

I did the following:

I first removed the `%` sign from the dict.

```
my_df = pd.DataFrame(my_dict.items())
ax = sns.barplot(x=0, y=1, data=my_df)
ax.set(xlabel = 'Cities', ylabel='%', title='Title')
```


[](https://i.stack.imgur.com/bpt8U.png)

[](https://i.stack.imgur.com/SmHro.png)


------------
    
    