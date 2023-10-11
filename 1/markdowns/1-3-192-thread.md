
# Post \#60978672 [Link](https://stackoverflow.com/questions/60978672/)

## Python: String to CamelCase

**Vote**: 8 (428/702) **Views**: 14681 (361/702) 

**Internal ID** \#1-3-192

Created at 2020-04-01 19:16:53

Tags: `python` `string`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

This is a question from Codewars:
Complete the method/function so that it converts dash/underscore delimited words into camel casing. The first word within the output should be capitalized only if the original word was capitalized (known as Upper Camel Case, also often referred to as Pascal case).

The input test cases are as follows:

```
test.describe("Testing function to_camel_case")
test.it("Basic tests")
test.assert_equals(to_camel_case(''), '', "An empty string was provided but not returned")
test.assert_equals(to_camel_case("the_stealth_warrior"), "theStealthWarrior", "to_camel_case('the_stealth_warrior') did not return correct value")
test.assert_equals(to_camel_case("The-Stealth-Warrior"), "TheStealthWarrior", "to_camel_case('The-Stealth-Warrior') did not return correct value")
test.assert_equals(to_camel_case("A-B-C"), "ABC", "to_camel_case('A-B-C') did not return correct value")
```


This is what I've tried so far:

```
def to_camel_case(text):
    str=text
    str=str.replace(' ','')
    for i in str:
        if ( str[i] == '-'):
            str[i]=str.replace('-','')
            str[i+1].toUpperCase()
        elif ( str[i] == '_'):
            str[i]=str.replace('-','')
            str[i+1].toUpperCase()
    return str
```


It passes the first two tests but not the main ones :

```
test.assert_equals(to_camel_case("the_stealth_warrior"), "theStealthWarrior", "to_camel_case('the_stealth_warrior') did not return correct value")
    test.assert_equals(to_camel_case("The-Stealth-Warrior"), "TheStealthWarrior", "to_camel_case('The-Stealth-Warrior') did not return correct value")
    test.assert_equals(to_camel_case("A-B-C"), "ABC", "to_camel_case('A-B-C') did not return correct value")
```


What am I doing wrong? 


----------
        
## Answer \#0

**Accepted** Vote: 13

Created at 2020-04-01 19:28:01

------------

You may have a working implementation with slight errors as mentioned in your comments, but I propose that you:

- split by the delimiters- apply a capitalization for all but the first of the tokens- rejoin the tokens

My implementation is:

```
def to_camel_case(text):
    s = text.replace("-", " ").replace("_", " ")
    s = s.split()
    if len(text) == 0:
        return text
    return s[0] + ''.join(i.capitalize() for i in s[1:])
```


IMO it makes a bit more sense.
The output from running tests is:

```
>>> to_camel_case("the_stealth_warrior")
'theStealthWarrior'
>>> to_camel_case("The-Stealth-Warrior")
'TheStealthWarrior'
>>> to_camel_case("A-B-C")
'ABC'
```



------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-10-08 12:39:35

------------

```
from re import sub

def to_camelcase(s):
  s = sub(r"(_|-)+", " ", s).title().replace(" ", "").replace("*","")
  return ''.join([s[0].lower(), s[1:]])

print(to_camelcase('some_string_with_underscore'))
print(to_camelcase('Some string with Spaces'))
print(to_camelcase('some-string-with-dashes'))
print(to_camelcase('some string-with dashes_underscores and spaces'))
print(to_camelcase('some*string*with*asterisks'))
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-04-01 19:31:59

------------

I think first of you should change the syntax a little because 'i' is a string not an integer. It should be

```
for i in str:
    if (i == '-'):
    ...
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-03-09 09:02:31

------------

this is my simple way
```
def to_camel_case(text):
    #your code herdlfldfde
    s = text.replace("-", " ").replace("_", " ")
    s = s.split()
    if len(text) == 0:
     return text
   return s[0] + ' '.join(s[1:]).title().replace(" ", "")
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-12-02 01:18:09

------------

Potential solution/package that supports pascal/snake conversion to camel.
```
# pip install camelCasing
from camelCasing import camelCasing as cc

for s in ['the_stealth_warrior', 'The-Stealth-Warrior', 'A-B-C']:
    print(cc.toCamelCase(s=s, user_acronyms=None))

theStealthWarrior
theStealthWarrior
ABC
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-12-08 14:11:58

------------

my  simple way:
```
def to_camel_case(text):
    return text[0]+text.title()[1:].replace("-","").replace("_","")
```



------------
    
    