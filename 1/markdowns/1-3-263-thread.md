
# Post \#57150426 [Link](https://stackoverflow.com/questions/57150426/)

## What is print(f"...")

**Vote**: 114 (48/702) **Views**: 326901 (19/702) 

**Internal ID** \#1-3-263

Created at 2019-07-22 16:46:51

Tags: `python`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am reading through a python script that takes an input of XML files and outputs an XML file. However, I do not understand the printing syntax. Can someone please explain what `f` in `print(f"...")` does?

```
args = parser.parser_args()

print(f"Input directory: {args.input_directory}")
print(f"Output directory: {args.output_directory}")
```



----------
        
## Answer \#0

**Accepted** Vote: 113

Created at 2019-07-22 16:52:17

------------

The `f` means [Formatted string literals](https://docs.python.org/3.6/reference/lexical_analysis.html#formatted-string-literals)  and it's new in `Python 3.6`.

---


> A  or  is a string literal that is
prefixed with `f` or `F`. These strings may contain replacement
fields, which are expressions delimited by curly braces `{}`. While
other string literals always have a constant value, formatted strings
are really expressions evaluated at run time.

---


Some examples of formatted string literals:
```
>>> name = "Fred"
>>> f"He said his name is {name}."
"He said his name is Fred."

>>> name = "Fred"
>>> f"He said his name is {name!r}."
"He said his name is Fred."

>>> f"He said his name is {repr(name)}." # repr() is equivalent to !r
"He said his name is Fred."

>>> width = 10
>>> precision = 4
>>> value = decimal.Decimal("12.34567")
>>> f"result: {value:{width}.{precision}}" # nested fields
result: 12.35

>>> today = datetime(year=2017, month=1, day=27)
>>> f"{today:%B %d, %Y}" # using date format specifier
January 27, 2017

>>> number = 1024
>>> f"{number:#0x}" # using integer format specifier
0x400
```



------------
    
    
## Answer \#1

 Vote: 43

Created at 2019-07-22 16:59:18

------------

In Python 3.6, the f-string, , was introduced([PEP 498](https://peps.python.org/pep-0498/)). In short, it is a way to format your string that is more readable and fast.
Example:
```
agent_name = 'James Bond'
kill_count = 9


# old ways
print("%s has killed %d enemies" % (agent_name,kill_count))

print('{} has killed {} enemies'.format(agent_name,kill_count))
print('{name} has killed {kill} enemies'.format(name=agent_name,kill=kill_count))
    

# f-strings way
print(f'{agent_name} has killed {kill_count} enemies')
```

The `f` or `F`  in front of strings tell Python to look at the values , expressions or instance inside {} and substitute them with the variables values or results if exists. The best thing about f-formatting is that you can do cool stuff in {}, e.g. `{kill_count * 100}`.
You can use it to debug using print e.g.
```
print(f'the {agent_name=}.')
# the agent_name='James Bond'
```

Formatting, such as zero-padding, float and percentage rounding is made easier:
```
print(f'{agent_name} shoot with {9/11 : .2f} or {9/11: .1%} accuracy')
# James Bond shoot with  0.82 or  81.8% accuracy
```

Even cooler is the ability to nest and format. Example date
```
from datetime import datetime

lookup = {
    '01': 'st',
    '21': 'st',
    '31': 'st',
    '02': 'nd',
    '22': 'nd',
    '03': 'rd',
    '23': 'rd'
}

print(f"{datetime.now(): %B %d{lookup.get('%B', 'th')} %Y}")

# April 14th 2022
```

Pretty formatting is also easier
```
tax = 1234

print(f'{tax:,}') # separate 1k \w comma
# 1,234

print(f'{tax:,.2f}') # all two decimals 
# 1,234.00

print(f'{tax:~>8}') # pad left with ~ to fill eight characters or < other direction
# ~~~~1234

print(f'{tax:~^20}') # centre and pad
# ~~~~~~~~1234~~~~~~~~
```

The `__format__` allows you to funk with this feature. Example
```
class Money:
    
    def __init__(self, currency='€'):
        self.currency = currency
        
    def __format__(self, value):
        
        return f"{self.currency} {float(value):.2f}"
        
        
tax = 12.34
money = Money(currency='$')
print(f'{money: {tax}}')
# $ 12.34
```

There is much more. Readings:
- [PEP 498 Literal String Interpolation](https://www.python.org/dev/peps/pep-0498/)- [Python String Formatting](https://realpython.com/python-string-formatting/)


------------
    
    
## Answer \#2

 Vote: 17

Created at 2019-07-22 16:52:00

------------

the f string is also known as the literal string to insert a variable into the string and make it part so instead of doing
```
x = 12
y = 10

word_string = x + ' plus ' + y + 'equals: ' + (x+y)
```

instead, you can do
```
x = 12
y = 10

word_string = f'{x} plus {y} equals: {x+y}'
output: 12 plus 10 equals: 22
```

this will also help with spacing due to it will do exactly as the string is written


------------
    
    
## Answer \#3

 Vote: 4

Created at 2021-08-03 02:09:34

------------

A string prefixed with `'f'` or `'F'` and writing expressions as `{expression}` is a way to format string, which can include the value of Python expressions inside it.
Take these code as an example:
```
def area(length, width):
    return length * width

l = 4
w = 5

print("length =", l, "width =", w, "area =", area(l, w))  # normal way
print(f"length = {l} width = {w} area = {area(l,w)}")     # Same output as above
print("length = {l} width = {w} area = {area(l,w)}")      # without f prefixed
```

Output:
```
length = 4 width = 5 area = 20
length = 4 width = 5 area = 20
length = {l} width = {w} area = {area(l,w)}
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2020-09-07 18:41:48

------------

```
args = parser.parser_args()

print(f"Input directory: {args.input_directory}")
print(f"Output directory: {args.output_directory}")
```

is the same as
```
print("Input directory: {}".format(args.input_directory))
print("Output directory: {}".format(args.output_directory))
```

it is also the same as
```
print("Input directory: "+args.input_directory)
print("Output directory: "+args.output_directory)
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2021-07-28 07:44:01

------------

f-string in python lets you format data for printing using string templates. 


```
name = 'Niroshan'
age  = 25;
print(f"Hello I'm {name} and {age} years young")
```

> Hello I'm Niroshan and 25 years young

---



```
name = 'Niroshan'
age  = 25;
print("Hello I'm {name} and {age} years young")
```

> Hello I'm {name} and {age} years young


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-11-10 16:15:57

------------

f function is Transferring data to other places in your content.
It mostly used Changeable Data.
class  Methods ():
def  (self ,F,L,E,S,T):
self.FirstName=F
self.LastName=L
self.Email=E
self.Salary=S
self.Time =T
```
def  Salary_Msg(self):
        #f is called function f
        #next use {write in}
        return f{self.firstname} {self.Lastname} earns AUD {self.Salary}per {self.Time} "
```



------------
    
    