
# Post \#61000501 [Link](https://stackoverflow.com/questions/61000501/)

## Json serialization of nested dataclasses

**Vote**: 14 (336/702) **Views**: 14524 (365/702) 

**Internal ID** \#1-3-287

Created at 2020-04-02 20:25:24

Tags: `python` `python-dataclasses` `json-serialization`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I would need to take the question about `json serialization of @dataclass` from [Make the Python json encoder support Python's new dataclasses](https://stackoverflow.com/questions/51286748/make-the-python-json-encoder-support-pythons-new-dataclasses)  a bit further: consider when they are in a nested structure. 

Consider:

```
import json
from attr import dataclass
from dataclasses_json import dataclass_json
@dataclass
@dataclass_json
class Prod:
    id: int
    name: str
    price: float

prods = [Prod(1,'A',25.3),Prod(2,'B',79.95)]
pjson = json.dumps(prods)
```


That gives us:

```
TypeError: Object of type Prod is not JSON serializable
```


Note the above does incorporate one of the answers [https://stackoverflow.com/a/59688140/1056563](https://stackoverflow.com/a/59688140/1056563) . It  claims to support the nested case via the `dataclass_json` decorator . Apparently that does not actually work. 

I also tried another of the answers [https://stackoverflow.com/a/51286749/1056563](https://stackoverflow.com/a/51286749/1056563) :

```
class EnhancedJSONEncoder(json.JSONEncoder):
        def default(s, o):
            if dataclasses.is_dataclass(o):
                return dataclasses.asdict(o)
            return super().default(o)
```


And I created a helper method for it:

```
def jdump(s,foo):
    return json.dumps(foo, cls=s.c.EnhancedJSONEncoder)
```


But using that method also did not effect the (error) result.  Any further tips?


----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2021-04-16 16:06:36

------------

You can use a `pydantic` library. From the example in [documentation](https://pydantic-docs.helpmanual.io/usage/exporting_models/)
```
from pydantic import BaseModel


class BarModel(BaseModel):
    whatever: int


class FooBarModel(BaseModel):
    banana: float
    foo: str
    bar: BarModel


m = FooBarModel(banana=3.14, foo='hello', bar={'whatever': 123})

# returns a dictionary:
print(m.dict())
"""
{
    'banana': 3.14,
    'foo': 'hello',
    'bar': {'whatever': 123},
}
"""
print(m.dict(include={'foo', 'bar'}))
#> {'foo': 'hello', 'bar': {'whatever': 123}}
print(m.dict(exclude={'foo', 'bar'}))
#> {'banana': 3.14}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2020-04-02 20:37:06

------------

This is actually not a direct answer but more of a reasonable workaround for cases where mutability is not needed (or desirable).  The `typing` based `NamedTuple` looks and feels quite similar and is probably the inspiration behind the `dataclass`. If serialization were needed it is likely presently the best alternative.

```
from typing import NamedTuple

class Prod(NamedTuple):
    id: str
    name: str
    price: str
```


I made that as a drop-in replacement for the `dataclass` based `Prod` class and it works.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-05-03 15:20:40

------------

```
import json
from dataclasses import dataclass, asdict


@dataclass
class Prod:
    id: int
    name: str
    price: float


prods = [asdict(Prod(1, 'A', 25.3)), asdict(Prod(2, 'B', 79.95))]
pjson = json.dumps(prods)
print(pjson)
# [{"id": 1, "name": "A", "price": 25.3}, {"id": 2, "name": "B", "price": 79.95}]
```



------------
    
    