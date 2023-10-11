
# Post \#66526297 [Link](https://stackoverflow.com/questions/66526297/)

## Python how to type anotate a method that returns self?

**Vote**: 9 (409/702) **Views**: 3084 (565/702) 

**Internal ID** \#1-3-212

Created at 2021-03-08 08:14:22

Tags: `python` `type-hinting` `python-3.9`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Suppose I have a class that implements method chaining:
```
from __future__ import annotations

class M:
    def set_width(self, width: int)->M:
        self.width = width
        return self

    def set_height(self, height: int)->M:
        self.height = height
        return self
```

I could use it like this:
```
box = M().set_width(5).set_height(10)
```

This works, but if I have a subclass M3D:
```
class M3D(M):
    def set_depth(self, depth: int) -> M3D:
        self.depth = depth
        return self
```

Now I can't do this:
```
cube = M3D().set_width(2).set_height(3).set_depth(5)
```

I get the following error in mypy:
```
_test_typeanotations.py:21: error: "M" has no attribute "set_depth"; maybe "set_width"
```

Because `set_width()` returns an `M` which has no method `set_depth`. I have seen suggestions to override `set_width()` and `set_height()` for every subclass to specify the correct types, but that would be a lot of code to write for each method. There has to be a easier way.
This is also relevant for special methods, for example `__enter__` traditionally returns `self`, so it would be nice to have a way to specify this without needing to even mention it in subclasses.


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-03-15 14:37:53

------------

After a lot of research and expirimentation, I have found a way that works in mypy, though Pycham still guesses the type wrong sometimes.
The trick is to make `self` a type var:
```
from __future__ import annotations

import asyncio
from typing import TypeVar

T = TypeVar('T')


class M:
    def set_width(self: T, width: int)->T:
        self.width = width
        return self

    def set_height(self: T, height: int)->T:
        self.height = height
        return self

    def copy(self)->M:
        return M().set_width(self.width).set_height(self.height)


class M3D(M):
    def set_depth(self: T, depth: int) -> T:
        self.depth = depth
        return self

box = M().set_width(5).set_height(10) # box has correct type
cube = M3D().set_width(2).set_height(3).set_depth(5) # cube has correct type
attemptToTreatBoxAsCube = M3D().copy().set_depth(4) # Mypy gets angry as expected
```

The last line specifically works fine in mypy but pycharm will still autocomplete `set_depth` sometimes even though `.copy()` actually returns an `M` even when called on a `M3D`.


------------
    
    
## Answer \#1

 Vote: 10

Created at 2022-08-03 18:00:13

------------

In Python 3.11 and its later versions, you will be able to do this:
```
from typing import Self

class M:
    def set_width(self, width: int) -> Self:
        self.width = width
        return self
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-03-08 08:54:20

------------

This is a classic problem in any language using inheritance. And it is handled differently by the languages:
- `set_height``set_depth`- 
Python is a dynamically typed language, so there is no cast instruction. So you are left with 3 possible ways:
- - - 
The following is only my opinion.
I would avoid the  if possible, because if you will leave warnings in your code, you would have to later control after each and every change if there is a new warning.
I would not override methods just to get rid of a warning. After all Python is a dynamically typed language that even allows duck typing. If I know that the code is correct I would avoid adding useless code (DRY and KISS principles)
SO I will just assume that comments to suspend annotation controls were invented for a reason and use them (what I call ).


------------
    
    