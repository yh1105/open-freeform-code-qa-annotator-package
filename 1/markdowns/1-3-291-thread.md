
# Post \#65486981 [Link](https://stackoverflow.com/questions/65486981/)

## What is __peg_parser__ in Python?

**Vote**: 59 (107/702) **Views**: 5413 (511/702) 

**Internal ID** \#1-3-291

Created at 2020-12-29 03:31:25

Tags: `python` `python-3.x` `python-3.9`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I was using the [keyword](https://docs.python.org/3/library/keyword.html) built-in module to get a list of all the keywords of the current Python version. And this is what I did:
```
>>> import keyword
>>> print(keyword.kwlist)
['False', 'None', 'True', '__peg_parser__', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

And in the [keyword.kwlist](https://docs.python.org/3/library/keyword.html#keyword.kwlist) list there is `__peg_parser__`. So to see what it does, I type `__peg_parser__` in a Python 3.9 interpreter on Windows (you'll get the same output on Mac OS and Linux as well), and this is what is get:
```
>>> __peg_parser__
  File "<stdin>", line 1
    __peg_parser__
    ^
SyntaxError: You found it!
```

So my question is, what is `__peg_parser__` and why do I get `SyntaxError: You found it!`?


----------
        
## Answer \#0

**Accepted** Vote: 36

Created at 2020-12-29 03:38:20

------------

It was an easter egg related to the rollout of [the new PEG parser](https://www.python.org/dev/peps/pep-0617/). The easter egg, along with the old LL(1) parser, [will be removed in 3.10](https://bugs.python.org/issue40939).


------------
    
    
## Answer \#1

 Vote: 14

Created at 2020-12-29 03:46:06

------------

Guido published on github [here](https://github.com/we-like-parsers/pegen) for the new PEG parser.
It's also on [Python PEP](https://www.python.org/dev/peps/pep-0617/).
As it mentions:
> This PEP proposes replacing the current LL(1)-based parser of CPython with a new PEG-based parser. This new parser would allow the elimination of multiple "hacks" that exist in the current grammar to circumvent the LL(1)-limitation. It would substantially reduce the maintenance costs in some areas related to the compiling pipeline such as the grammar, the parser and the AST generation. The new PEG parser will also lift the LL(1) restriction on the current Python grammar.
Also mentioned in [Python 3.9 What's new](https://docs.python.org/3/whatsnew/3.9.html#new-parser) page.
In Python 3.10, the `LL(1)` parser will be removed. Python 3.9 uses a new parser, based on PEG instead of LL(1).
In Python 3.6, it is not defined:
```
>>> __peg_parser__
Traceback (most recent call last):
  File "<pyshell#13>", line 1, in <module>
    __peg_parser__
NameError: name '__peg_parser__' is not defined
>>>
```



------------
    
    
## Answer \#2

 Vote: 10

Created at 2020-12-29 03:43:41

------------


### What is __peg_parser__?


It is an Easter Egg in Python (of the [Peg Parser](https://www.python.org/dev/peps/pep-0617/)) for when the new [Peg Parser](https://www.python.org/dev/peps/pep-0617/) was released. As mentioned in this [discussion](https://bugs.python.org/issue40939), it will be removed in Python 3.10.
Before the Easter Egg was called `__new_parser__`, but was changed to `__peg_parser__`, to make it future proof as mentioned in the [message](https://bugs.python.org/msg371265):
> `new`, `ex` or `ng` are not really future proof names. Can we rename the keyword to `__peg_parser__`?

### Why do you get SyntaxError: You found it!?


You get `SyntaxError: You found it!` because it is part of the Easter Egg.

### Will it be removed in the future?


Since the [LL(1) parser](https://en.wikipedia.org/wiki/LL_parser) will be replaced be the new [Peg Parser](https://www.python.org/dev/peps/pep-0617/), it will be removed in Python 3.10.

### __peg_parser__ in Earlier and Later Versions of Python


It didn't exist in earlier versions of Python.
Python 3.8 and earlier:
```
>>> __peg_parser__
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name '__peg_parser__' is not defined
```

Python 3.9:
```
>>> __peg_parser__
  File "<stdin>", line 1
    __peg_parser__
    ^
SyntaxError: You found it!
```

Python 3.10:
```
>>> __peg_parser__
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name '__peg_parser__' is not defined
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-01-18 05:24:22

------------

This PEP proposes replacing the current LL(1)-based parser of CPython with a new PEG-based parser. This new parser would allow the elimination of multiple "hacks" that exist in the current grammar to circumvent the LL(1)-limitation. It would substantially reduce the maintenance costs in some areas related to the compiling pipeline such as the grammar, the parser and the AST generation. The new PEG parser will also lift the LL(1) restriction on the current Python grammar.
[read more](https://github.com/we-like-parsers/pegen)


------------
    
    