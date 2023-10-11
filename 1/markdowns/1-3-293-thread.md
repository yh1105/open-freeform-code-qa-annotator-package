
# Post \#67136061 [Link](https://stackoverflow.com/questions/67136061/)

## TypeError: list indices must be integers or slices, not str - While accessing element of list

**Vote**: 1 (672/702) **Views**: 15024 (357/702) 

**Internal ID** \#1-3-293

Created at 2021-04-17 08:11:31

Tags: `python` `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm new to Python. Here I'm trying to access `list` object in python which is throwing following error.
> TypeError: list indices must be integers or slices, not str

---


`print(a)`

```
['{"id":9,"name":"Foo"}']
```

`print(type(a))`

```
<class 'list'>
```

`print(a['id'])`

```
TypeError: list indices must be integers or slices, not str
```

`print(a[0]['id'])`

```
TypeError: string indices must be integers
```

`print(a[0])`

```
{"id":9,"name":"Foo"}
```


---



### Expected Output


```
9
```



----------
        
## Answer \#0

**Accepted** Vote: 0

Created at 2021-04-17 09:26:02

------------

I was solve this by the following

1. Passing [{"id":9,"name":"Foo"}] instead of ['{"id":9,"name":"Foo"}'] which can done by removing json_encode() while sending data to Flask Application. In my case was from Laravel / PHP.
2. By accessing element using a[0]['id'] as pointed by @mhhabib




------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-04-17 08:16:36

------------

First of all, you have taken the dictionary mistakenly with ' ' which indicates `'{"id": 9, "name": "Foo"}'` as a string. Remove quotation marks and iterate the list. Assume the list size is fixed to one dictionary list.
```
a = [{"id":9, "name":"Foo"}]

id = a[0]['id']
print(id)
```

Let's your list be long like
```
a = [{"id": 9, "name": "Foo"}, {"id": 10, "name": "Bar"},{"id": 11, "name": "FooBar"}]
```

Then get id by using loop
```
for l in a:
    print(l['id'])
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-04-17 08:22:28

------------

you are storing the dictionary as a string so the error you are getting is expected.
If you want to access the id in the dictionary then store it as the dictionary itself.
To access the id value you can check the below code
```
a=[{"id":9,"name":"Foo"}]
b=a[0]['id']
print(b)
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-04-17 08:26:25

------------

list have not attribute allows key and value.
then you can try with dictonary.


------------
    
    