
# Post \#57011986 [Link](https://stackoverflow.com/questions/57011986/)

## How to check that a string contains only “a-z”, “A-Z” and “0-9” characters

**Vote**: 10 (387/702) **Views**: 58281 (144/702) 

**Internal ID** \#1-3-208

Created at 2019-07-12 18:00:21

Tags: `python` `regex` `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python` `regex`)

----------

**Notepad**


----------

I am importing string and trying to check if text contains only "a-z", "A-Z", and "0-9".

But I get only input and it doesn't print success when I enter letters and digits

```
import string
text=input("Enter: ")
correct = string.ascii_letters + string.digits
if text in correct:
    print("Success")
```



----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2019-07-12 18:04:33

------------

You could use regex for this, e.g. check string against following pattern:

```
import re
pattern = re.compile("[A-Za-z0-9]+")
pattern.fullmatch(string)
```




`[A-Za-z0-9]` matches a character in the range of A-Z, a-z and 0-9, so letters and numbers.

`+` means to match 1 or more of the preceeding token.

The `re.fullmatch()` method allows to check if the whole string matches the regular expression pattern. Returns a corresponding match object if match found, else returns `None` if the string does not match the pattern.

All together:

```
import re

if __name__ == '__main__':
    string = "YourString123"
    pattern = re.compile("[A-Za-z0-9]+")

    # if found match (entire string matches pattern)
    if pattern.fullmatch(string) is not None:
        print("Found match: " + string)
    else:
        # if not found match
        print("No match")
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2019-07-12 18:22:35

------------

Just use [str.isalnum()](https://docs.python.org/3/library/stdtypes.html#str.isalnum)

```
>>> '123AbC'.isalnum()
True
>>> '1&A'.isalnum()
False
```


Referencing the docs:

> Return true if all characters in the string are alphanumeric and there
  is at least one character, false otherwise. A character `c` is alphanumeric 
  if one of the following returns `True`: `c.isalpha()`, `c.isdecimal()`,
  `c.isdigit()`, or `c.isnumeric()`.

If you don't want `str.isdigit()` or `str.isnumeric()` to be checked which may allow for decimal points in digits just use `str.isnumeric()` and `str.isalpha()`:

```
>>> all(c.isnumeric() or c.isalpha() for c in '123AbC')
True
>>> all(c.isnumeric() or c.isalpha() for c in '1&A')
False
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-07-13 15:07:24

------------

You must compare each letter of the incoming text separately.

```
import string
text = input("Enter: ")
correct = string.ascii_letters + string.digits
status = True
for char in text:
    if char not in correct:
        status = False
if status:
    print('Correct')
else:
    print('InCorrect')
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2019-07-12 18:05:03

------------

You are testing if the entire string is in the string `'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'`

So say the input is `'testInput'`

You are checking if `'testInput'` is in the string `'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'` which it is not.

You need to check each character individually

You can do this with a function for ease of use

```
import string
def validCharacters(text):
    correct = string.ascii_letters + string.digits
    for character in text:
         if character not in correct:
              return False

    return True

text = input("Enter: ")
if validCharacters(text):
     print("Correct")
else:
     print("Incorrect")
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2019-07-12 18:09:04

------------

We can remove all characters that aren't A-Z, a-z, or 0-9 then check the length of the remaining string. If it's greater than 0, there are characters that aren't the ones above:

```
import re
text = input("Enter: ")
result = re.sub("[A-Za-z0-9]", '', text)
if len(result) == 0:
    print("Success")
else:
    print("Failure")
```


Result:

```
abcDEF123 -> Success
hello! -> Failure
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2019-07-12 18:15:09

------------

You can do it in many various ways, I would harness `set`s for that following way:

```
import string
correct = {char for char in string.ascii_letters + string.digits}
def is_correct(text):
    return {char for char in text}.issubset(correct)
print(is_correct('letters123')) # True
print(is_correct('???')) # False
print(is_correct('\n')) # False
```


I simply check if `set` consisting of characters of given text is subset of `set` of all legal characters. Keep in mind that this always process whole text, which is not best from performance point of view (as you might end check after you find first illegal character), but it should not be problem unless you need to deal with very long texts or in very small amount of time.


------------
    
    
## Answer \#6

 Vote: -1

Created at 2019-07-12 18:08:57

------------

You can use regex match for checking if the string contains only alpha and letters

```
import re
 text = input("enter:")
 regex = r"([0-9a-zA-Z]+)"

 match = re.match(regex, string)

 if match != None:
    print("success")
```



------------
    
    