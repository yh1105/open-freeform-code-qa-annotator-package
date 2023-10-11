
# Post \#71279525 [Link](https://stackoverflow.com/questions/71279525/)

## How to increment index in Python loop

**Vote**: 1 (672/702) **Views**: 4935 (524/702) 

**Internal ID** \#1-3-288

Created at 2022-02-26 19:04:40

Tags: `python` `list` `loops` `iteration` `indices`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am wanting to loop through a string and capture 2 items each time while also incrementing through the index of the iterable. So I want to slice 2 items but increase the index by 1 every time through the loop. How can I do this?
my_string = 'teststring'
desired output =
te
es
st
ts
st
tr
ri
in
ng
I have tried the following to slice the two items, but can't figure out the best way to iterate thought the index
```
str1 = 'teststring'
i=0
while i<10: 
    i +=1
    str2=str1[0:2]
    print(str2)
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-02-26 20:12:00

------------

By using [list comprehension](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions) you can do this in a one-liner
```
s = 'teststring'

r = ' '.join([s[i:i+2] for i in range(len(s)-1)])

print(r)
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-02-26 19:09:25

------------

Here is a possible solution (`s` is your string):
```
for j in range(len(s) - 1):
    print(s[j:j + 2])
```

Another one:
```
for c1, c2 in zip(s[:-1], s[1:]):
    print(c1 + c2)
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-26 19:09:58

------------

```
str1 = 'teststring'
result = []
for i in range(len(str1) - 1):
    result.append(str1[i:i + 2])

print(result)
```

output
```
['te', 'es', 'st', 'ts', 'st', 'tr', 'ri', 'in', 'ng']
```



------------
    
    
## Answer \#3

 Vote: -1

Created at 2022-02-26 19:12:51

------------

Since you are trying to move both the start and end point of the slice with each iteration, you want to use the index+length of slice for the end point.
You should iterate after the slice is done. Here is the answer with minimal changes to your code:
```
str1 = 'teststring'
i=0
while i<=len(str1)-2: 
    str2=str1[i:i+2]
    i += 1
    print(str2)
```



------------
    
    
## Answer \#4

 Vote: -1

Created at 2022-02-26 19:17:51

------------

I would use a for loop instead of a while loop, like this:
```
def strangeSlice(string):
    out = ""
    for i in range(len(string)-1):
        out += string[i:i+2]
        if i != len(string)-2:
            out += " "
    print(out)
    return out
def main():
    strangeSlice("teststring")
main() #you need to call the main function to run
```



------------
    
    