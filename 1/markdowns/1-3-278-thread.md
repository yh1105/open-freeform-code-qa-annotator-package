
# Post \#61338539 [Link](https://stackoverflow.com/questions/61338539/)

## How to use enum value in asdict function from dataclasses module

**Vote**: 26 (219/702) **Views**: 22270 (281/702) 

**Internal ID** \#1-3-278

Created at 2020-04-21 07:55:54

Tags: `python` `enums` `python-dataclasses`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a dataclass with a field `template` of type Enum. When using the `asdict` function it converts my dataclass to a dictionary. Is it possible to use the [value](https://docs.python.org/3/library/enum.html#programmatic-access-to-enumeration-members-and-their-attributes) attribute of `FoobarEnum` to return the string value instead of the Enum object?

My initial idea was to use the [dict_factory=dict](https://docs.python.org/3/library/dataclasses.html#dataclasses.asdict) parameter of the `asdict` function and provide my own factory but I couldn't figure out how to do this.

```
from dataclasses import dataclass, asdict
from enum import Enum


@dataclass
class Foobar:
  name: str
  template: "FoobarEnum"


class FoobarEnum(Enum):
  FIRST = "foobar"
  SECOND = "baz"


foobar = Foobar(name="John", template=FoobarEnum.FIRST)

print(asdict(foobar))
```


Current output:

```
{'name': 'John', 'template': <FoobarEnum.FIRST: 'foobar'>}
```


Goal:

```
{'name': 'John', 'template': 'foobar'}
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-04-21 10:43:02

------------

This can't be done with standard library except maybe by some metaclass enum hack I'm not aware of. `Enum.name` and `Enum.value` are builtin and not supposed to be changed.

The approach of using the dataclass `default_factory` isn't going to work either. Because `default_factory` is called to produce default values for the dataclass members, not to customize access to members.

You can either have the Enum member or the Enum.value as a dataclass member, and that's what `asdict()` will return. 

If you want to keep an Enum member -not just the Enum.value- as a dataclass member, and have a function converting it to dictionary that returns the Enum.value instead of the Enum member, the correct way to do it is implementing your own method to return the dataclass as a dictionary.

```
from dataclasses import dataclass
from enum import Enum


class FoobarEnum(Enum):
    FIRST = "foobar"
    SECOND = "baz"


@dataclass
class Foobar:
    name: str
    template: FoobarEnum

    def as_dict(self):
        return {
            'name': self.name,
            'template': self.template.value
        }


# Testing.
print(Foobar(name="John", template=FoobarEnum.FIRST).as_dict())
# {'name': 'John', 'template': 'foobar'}
```



------------
    
    
## Answer \#1

 Vote: 22

Created at 2020-11-05 08:45:17

------------

Actually you can do it.  has keyword argument [dict_factory](https://docs.python.org/3.8/library/dataclasses.html#dataclasses.asdict) which allows you to handle your data there:
```
from dataclasses import dataclass, asdict
from enum import Enum


@dataclass
class Foobar:
  name: str
  template: "FoobarEnum"


class FoobarEnum(Enum):
  FIRST = "foobar"
  SECOND = "baz"


def custom_asdict_factory(data):

    def convert_value(obj):
        if isinstance(obj, Enum):
            return obj.value
        return obj

    return dict((k, convert_value(v)) for k, v in data)


foobar = Foobar(name="John", template=FoobarEnum.FIRST)

print(asdict(foobar, dict_factory=custom_asdict_factory))
# {'name': 'John', 'template': 'foobar'}
```



------------
    
    
## Answer \#2

 Vote: 9

Created at 2020-11-05 07:47:42

------------

```
from dataclasses import dataclass, asdict
from enum import Enum


class FoobarEnum(Enum):
    FIRST = "foobar"
    SECOND = "baz"


@dataclass
class Foobar:
    name: str
    template: FoobarEnum


def my_dict(data):

    return {
        field: value.value if isinstance(value, Enum) else value
        for field, value in data
    }


foobar = Foobar(name="John", template=FoobarEnum.FIRST)

data = {'name': 'John', 'template': 'foobar'}

assert asdict(foobar, dict_factory=my_dict) == data
```



------------
    
    
## Answer \#3

 Vote: 7

Created at 2022-03-29 18:00:47

------------

I had a similar issue where I needed to serialize my dataclass object to JSON and solved it by adding `str` as the first class FoobarEnum inherits from:
```
import json
from dataclasses import dataclass, asdict
from enum import Enum


@dataclass
class Foobar:
  name: str
  template: "FoobarEnum"


class FoobarEnum(str, Enum):
  FIRST = "foobar"
  SECOND = "baz"


foobar = Foobar(name="John", template=FoobarEnum.FIRST)

print(json.dumps(asdict(foobar)))
```

It doesn't change the behavior of `asdict`, but I can serialize the object now.
Reference: [Serialising an Enum member to JSON](https://stackoverflow.com/questions/24481852/serialising-an-enum-member-to-json)


------------
    
    
## Answer \#4

 Vote: 2

Created at 2020-10-30 04:56:38

------------

You can implement the `__deepcopy__` method to achieve what you want:
```
class FoobarEnum(Enum):
  FIRST = "foobar"
  SECOND = "baz"

  def __deepcopy__(self, memo):
      return self.value
```

The `asdict` function handles dataclasses, tuples, lists and dicts. In case of any other type it calls:
`copy.deepcopy(obj)`
Overriding `__deepcopy__` like this is probably not the best idea though.


------------
    
    
## Answer \#5

 Vote: 2

Created at 2020-11-03 19:31:24

------------

Add `__post_init__` like below.
```
from dataclasses import dataclass, asdict
from enum import Enum
from typing import Union


@dataclass
class Foobar:
    name:str
    template: Union["FoobarEnum", str]
    def __post_init__(self):
        self.template = self.template.value

class FoobarEnum(Enum):
    FIRST = "foobar"
    SECOND = "baz"

foobar = Foobar(name="John", template=FoobarEnum.FIRST)
print(asdict(foobar))
```

Depending on the usecase, you can inherit from both `str` and `Enum` or have a init only field.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-11-04 22:31:00

------------

Have you tried this?
```
import json

def dumps(object):
    def default(o):
        if isinstance(o, Enum):
            # use enum value when JSON deserialize the enum
            return o.__dict__['_value_'] 
        else:
            return o.__dict__
    return json.dumps(object, default=default)

print(json.dumps(YOUR_OBJECT_CONTAINS_ENUMS, default=default))
```



------------
    
    