
# Post \#62011741 [Link](https://stackoverflow.com/questions/62011741/)

## Pydantic: dataclass vs BaseModel

**Vote**: 37 (163/702) **Views**: 27110 (246/702) 

**Internal ID** \#1-3-269

Created at 2020-05-25 22:28:38

Tags: `python` `pydantic`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

What are the advantages and disadvantages of using Pydantic's dataclass vs BaseModel? Are there any performance issues or is it easier to Pydantic's dataclass in the other python module?


----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2020-05-25 23:24:00

------------

Your question is answered in Pydantic's [documentation](https://pydantic-docs.helpmanual.io/usage/dataclasses/), specifically:

> Keep in mind that `pydantic.dataclasses.dataclass` is a drop-in replacement for `dataclasses.dataclass` with validation,  a replacement for `pydantic.BaseModel` (with a small difference in how initialization hooks work). There are cases where subclassing `pydantic.BaseModel` is the better choice.For more information and discussion see [samuelcolvin/pydantic#710](https://github.com/samuelcolvin/pydantic/issues/710).

The discussion link will give you some of the context you are looking for. In general, Pydantic's `BaseModel` implementation is not bound to behave the same as Python's `dataclass` implementation. The example cited in the issue above is one good example:

```
from pydantic import BaseModel
from pydantic.dataclasses import dataclass
from typing import List

@dataclass
class A:
    x: List[int] = []

# Above definition with a default of `[]` will result in:
#   ValueError: mutable default <class 'list'> for field x is not allowed: use default_factory
# If you resolve this, the output will read as in the comments below.

class B(BaseModel):
    x: List[int] = []

print(A(x=[1, 2]), A(x=[3, 4])) # Output: A(x=[1, 2]) A(x=[3, 4])
print(B(x=[1, 2]), B(x=[3, 4])) # Output: x=[1, 2] x=[3, 4]
```


If what you want first and foremost is `dataclass` behavior and then to simply augment it with some Pydantic validation features, the `pydantic.dataclasses.dataclass` approach may be what you want. Otherwise, `BaseModel` is probably what you want.


------------
    
    
## Answer \#1

 Vote: 11

Created at 2020-12-14 12:07:43

------------

The `__repr__` string representation function for BaseModel is different from that of dataclass:
```
@dataclass()
    class Foo:
        number: int
    
    class Bar(BaseModel):
        number: int
    
    f = Foo(number = 1.4)
    b = Bar(number = 1.4)
    print(f)
    print(b)
```

Output:
```
Foo(number=1.4)
number=1
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2023-01-23 23:16:20

------------

Another option is to use [openapi-json-schema-generator](https://github.com/openapi-json-schema-tools/openapi-json-schema-generator) with model generation only. It allows robust validation of many python data types against openapi/json schemas. It
- - - - - [generate many use case tests](https://github.com/openapi-json-schema-tools/openapi-json-schema-generator/tree/master/samples/openapi3/client/3_0_3_unit_test/python/docs/apis/tags)- [https://github.com/openapi-json-schema-tools/openapi-json-schema-generator/tree/master/samples/openapi3/client/3_0_3_unit_test/python/test](https://github.com/openapi-json-schema-tools/openapi-json-schema-generator/tree/master/samples/openapi3/client/3_0_3_unit_test/python/test)- [CI tests are here](https://app.circleci.com/pipelines/github/openapi-json-schema-tools/openapi-json-schema-generator?branch=master)- 


------------
    
    