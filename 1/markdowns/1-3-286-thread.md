
# Post \#71115542 [Link](https://stackoverflow.com/questions/71115542/)

## How to repeat the input until a special condition is met in Python?

**Vote**: 3 (575/702) **Views**: 2606 (586/702) 

**Internal ID** \#1-3-286

Created at 2022-02-14 17:04:14

Tags: `python` `user-input` `try-except`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I need to take integer inputs from the user and add them to a set. The number of integers is unknown. The input process will end when the user input is "Done".
Here is my code:
```
s = set()
print('Please type the number, when you are done please type "Done":')
while True:
    try:
        a = int(input())
        s.add(a)
    except:
        if a == "Done":
            break
        else:
            print('Integer only, please re-type:')
            continue
print(s)
```

But it didn't work as intended.
My idea is simple. If the user types an integer then it's fine, just add to set `s`. If they type a string (which `int(input()` is wrong), then it will go down to `except`, if the string is "Done", then break the `while True` loop, if it isn't then ask the user to re-type and continue the loop.
Here is the error if I type a string first and if I type normally:
[](https://i.stack.imgur.com/yYTiP.png)
I tried to use a simple if-else statement at the start but it causes traceback right at `int(input())`.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2022-02-14 17:11:53

------------

When the exception happens, `a` remains unset. Try this instead.
```
s = set()
# Notice also correct quoting
print("Please type the number, when you're done please type 'Done':")
while True:
    a = input()
    try:
        n = int(a)
        s.add(n)
    # Avoid blanket except
    except ValueError:
        if a == "Done":
            break
        else:
            print('Integer only, please re-type:')
            continue
print(s)
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2022-02-14 17:09:11

------------

Use the two-argument form of `iter` to read the input until `Done` is entered. You can iterate over the resulting iterator, handling integer conversions and updating the set in the body of a `for` loop.
```
s = set()
def get_input():
    return input('''Please type the number, when you're done please type "Done": ''')

for x in iter(get_input, "Done"):
    # x is guaranteed to *not* be Done here, or the loop
    # would have already exited.
    try:
        a = int(x)
    except ValueError:
        print("Integer only, please re-type")
        continue
    s.add(a)
```

If you weren't concerned about error handling, you could reduce this to a single set comprehension:
```
s = {int(x) for x in iter(get_input, "Done")}
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-02-14 17:18:53

------------

Take the input as a string first and then convert it to integer before adding to the set. This way you can check if the value entered by the user is "Done" and exit the loop if it is.
Something like the code below. Please also check some syntax errors that you have in your code.
```
s = {}
print('Please type the number, when you\'re done please type "Done":')
while True:
    a = input()
    if a == 'Done':
        break
    else:
        try:
            s.add(int(a))
        except:
            print('Integer only, please re-type:')
print(s)
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-02-14 17:32:46

------------

You can try the code below.
```
s = set()
print('Please type the number, when you\'re done please type "Done":')
while True:
    try:
        a = input()
        if a == "Done":
            break
        s.add(int(a))
    except:
        if a == "Done":
            break
        else:
            print('Integer only, please re-type:')
            continue
print(s)
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-02-14 17:23:40

------------

```
s = set()
b = True

print("Please type the number, when you're done please type 'Done'.")

while b:
    try:
        a = input("Input: ")
        if a == "Done":
            b = False
            break
        a = int(a)
        s.add(a)
    except ValueError:
        print("Whoops, please type an integer!")
print(s)
```



------------
    
    