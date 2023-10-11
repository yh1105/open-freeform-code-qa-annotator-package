
# Post \#69506042 [Link](https://stackoverflow.com/questions/69506042/)

## Generating random characters in Python

**Vote**: 3 (575/702) **Views**: 3722 (552/702) 

**Internal ID** \#1-3-249

Created at 2021-10-09 10:51:08

Tags: `python` `python-3.x` `random`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I would like to generate a 16 character code
The code has 5 known characters
The code has 3 digits
The code must be random
What I did :
```
result1 = "NAA3U" + ''.join((random.choice(string.ascii_uppercase + string.digits) for codenum in range(11)))
```



----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2021-10-09 11:11:29

------------

One approach:
```
import random
import string

# select 2 digits at random
digits = random.choices(string.digits, k=2)

# select 9 uppercase letters at random
letters = random.choices(string.ascii_uppercase, k=9)

# shuffle both letters + digits
sample = random.sample(digits + letters, 11)

result = "NAA3U" + ''.join(sample)
print(result)
```

 
```
NAA3U6MUGYRZ3DEX
```

If the code needs to contain at least 3 digits, but is not limited to this threshold, just change to this line:
```
# select 11 uppercase letters and digits at random
letters = random.choices(string.ascii_uppercase + string.digits, k=11)
```

this will pick at random from uppercase letters and digits.


------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-10-09 10:57:17

------------

You can add the remaining 2 digits at the end if it is fine for you like this
```
import random
import string

result1 = "NAA3U" + ''.join((random.choice(string.ascii_uppercase) for codenum in range(8))) + str(random.randint(10,99))

print(result1)

NAA3URYWMGIHG45
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-10-09 11:21:32

------------

You can use `random.choices` and `random.shuffle` then use `''.join` like below:
```
>>> import random
>>> import string

>>> def generate_rndm():
...    digit_char = random.choices(string.ascii_uppercase, k=9) + random.choices(string.digits, k=2)
...    random.shuffle(digit_char)
...    return "NAA3U" + ''.join(digit_char)
```

Output:
```
>>> generate_rndm()
'NAA3UTVQG8DT8NRM'

>>> generate_rndm()
'NAA3UCYBWCNQ45HR'

>>> generate_rndm()
'NAA3UIJP7W7DLOCQ'
```



------------
    
    