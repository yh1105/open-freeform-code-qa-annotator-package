
# Post \#71205405 [Link](https://stackoverflow.com/questions/71205405/)

## Write a Unit Test for function without return value

**Vote**: 0 (696/702) **Views**: 2404 (590/702) 

**Internal ID** \#1-3-237

Created at 2022-02-21 11:17:12

Tags: `python` `python-3.x` `unit-testing`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a function that takes a link as input, then requests values, then puts them in an available list, this function doesn't return anything, how can I write test cases for them?
For example:
```
list = []
def myfunc(link):
    [code block]
    list.append(value)
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2022-02-21 12:18:32

------------

I don't think it is very clean, but this would work:
I made a new file to separate the production code from the testing code.
I imported the list (in my code it's l) and test on it.
My main.py:
```
from typing import Any

l = []

def myfunc(link: Any) -> None:
    my_actual_value = "my_expected_value"
    l.append(my_actual_value)
```

My main_test.py:
```
import unittest

import main
from main import myfunc

class TestMyFunc(unittest.TestCase):
    def setUp(self) -> None:
        main.l = []

    def test_my_func(self) -> None:
        # Given
        some_link = "some_link"
        my_expected_value = ["my_expected_value"]
        # When
        myfunc(some_link)
        # Then
        assert my_expected_value == main.l

    def test_my_func2(self) -> None:
        # Given
        some_link = "some_link2"
        my_expected_value = ["my_expected_value"]
        # When
        myfunc(some_link2)
        # Then
        assert my_expected_value == main.l
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-02-21 11:23:23

------------

Generally it's not great to use global variables. I'd recommend using something like a class. eg:
```
class Whatevs:
    def __init__(self):
        self.list = []

    def my_func(self,link):
        [code block]
        self.list.append(value)
```

Then in your test you can do something like:
```
instance = Whatevs()
instance.my_func()
assert instance.list == [whatever it should be]
```

Also, I'd recommend reading PEP8. It just has some stuff about naming that you might find useful


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-21 11:45:45

------------

Import the list, clear it, run the function and then check it's value:
```
from my_module import list, myfunc

def test_myfunc():
  list.clear()
  myfunc()
  assert list == [1, 2, 3]
```



------------
    
    