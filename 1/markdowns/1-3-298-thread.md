
# Post \#67083039 [Link](https://stackoverflow.com/questions/67083039/)

## Why does Python return [15] for [0xfor x in (1, 2, 3)]?

**Vote**: 70 (95/702) **Views**: 9308 (425/702) 

**Internal ID** \#1-3-298

Created at 2021-04-13 22:12:01

Tags: `python` `python-3.x` `operator-precedence` `short-circuiting`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

When running the following line:
```
>>> [0xfor x in (1, 2, 3)]
```

I expected Python to return an error.
Instead, the REPL returns:
> `[15]`
What can possibly be the reason?


----------
        
## Answer \#0

**Accepted** Vote: 103

Created at 2021-04-13 22:12:01

------------


### TL;DR


Python reads the expression as `[0xf or (x in (1, 2, 3))]`, because:

1. The Python tokenizer.
2. Operator precedence.


It never raises `NameError` thanks to [short-circuit evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation) - if the expression left to the `or` operator is a truthy value, Python will never try to evaluate the right side of it.

### Parsing hexadecimal numbers


First, we have to understand how Python reads hexadecimal numbers.
On [tokenizer.c](https://github.com/python/cpython/blob/master/Parser/tokenizer.c)'s huge `tok_get` function, we:

1. Find the first 0x.
2. Keep reading the next characters as long as they're in the range of 0-f.


The parsed token, `0xf` (as "o" is not in the range of 0-f), will eventually get passed to the PEG parser, which will convert it to the decimal value `15` (see Appendix A).
We still have to parse the rest of the code, `or x in (1, 2, 3)]`, which leaves as with the following code:
```
[15 or x in (1, 2, 3)]
```


### Operator precedence


Because `in` have higher [operator precedence](https://docs.python.org/3/reference/expressions.html#operator-precedence) than `or`, we might expect `x in (1, 2, 3)` to evaluate first.
That is troublesome situation, as `x` doesn't exist and will raise a `NameError`.

### or is lazy


Fortunately, Python supports [Short-circuit evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation) as `or` is a lazy operator: if the left operand is equivalent to `True`, Python won't bother evaluating the right operand.
We can see it using the `ast` module:
```
parsed = ast.parse('0xfor x in (1, 2, 3)', mode='eval')
ast.dump(parsed)
```

Output:
```
Expression(
        body=BoolOp(
            op=Or(),
            values=[
                Constant(value=15),   # <-- Truthy value, so the next operand won't be evaluated.
                Compare(
                    left=Name(id='x', ctx=Load()),
                    ops=[In()],
                    comparators=[
                        Tuple(elts=[Constant(value=1), Constant(value=2), Constant(value=3)], ctx=Load())
                    ]
                )
            ]
        )
    )
```

So the final expression is equal to `[15]`.

---



### Appendix A: The PEG parser


On [pegen.c](https://github.com/python/cpython/blob/master/Parser/pegen.c#L947)'s `parsenumber_raw` function, we can find how Python treats leading zeros:
```
if (s[0] == '0') {
        x = (long)PyOS_strtoul(s, (char **)&end, 0);
        if (x < 0 && errno == 0) {
            return PyLong_FromString(s, (char **)0, 0);
        }
    }
```

[PyOS_strtoul](https://github.com/python/cpython/blob/master/Python/mystrtoul.c#L212) is in `Python/mystrtoul.c`.
Inside mystrtoul.c, the parser looks at [one character after the 0x](https://github.com/python/cpython/blob/master/Python/mystrtoul.c#L110). If it's an hexadecimal character, Python sets the base of the number to be 16:
```
if (*str == 'x' || *str == 'X') {
                /* there must be at least one digit after 0x */
                if (_PyLong_DigitValue[Py_CHARMASK(str[1])] >= 16) {
                    if (ptr)
                        *ptr = (char *)str;
                    return 0;
                }
                ++str;
                base = 16;
            } ...
```

Then it [parses](https://github.com/python/cpython/blob/master/Python/mystrtoul.c#L212) the rest of the number as long as the characters are in the range of 0-f:
```
while ((c = _PyLong_DigitValue[Py_CHARMASK(*str)]) < base) {
        if (ovlimit > 0) /* no overflow check required */
            result = result * base + c;
        ...
        ++str;
        --ovlimit;
    }
```

[Eventually](https://github.com/python/cpython/blob/master/Python/mystrtoul.c#L240), it sets the pointer to point the last character that was scanned - which is one character past the last hexadecimal character:
```
if (ptr)
        *ptr = (char *)str;
```


---



### Thanks


- [CSI_Tech_Dept](https://www.reddit.com/r/Python/comments/mqfb67/why_does_python_return_15_for_0xfor_x_in_1_2_3/gufu5yo/?utm_source=reddit&utm_medium=web2x&context=3)- [The original Tweet](https://twitter.com/nedbat/status/1382027078816653319)


------------
    
    
## Answer \#1

 Vote: 28

Created at 2021-04-14 16:14:47

------------

Other answers already tell what exactly happens. But for me, the interesting part was that the operator is recognized even without whitespace between the number and it. Actually, my first thought was "Wow, Python has a weird parser".
But before judging too harshly, maybe I should ask my other friends what they think:
Perl:
```
$ perl -le 'print(0xfor 3)'
15
```

Lua:
```
$ lua5.3 -e 'print(0xfor 4)'
15
```

Awk doesn't have `or`, but it has `in`:
```
$ awk 'BEGIN { a[15]=1; print(0x0fin a); }'
1
```

Ruby? (I don't really know it, but let's guess):
```
$ ruby -e 'puts 0x0for 5'
15
```

Yep, FWIW, Python is not alone, all of those other script-type languages also recognize the alphabetic operators even if stuck immediately to the back of a numeric constant.


------------
    
    
## Answer \#2

 Vote: 6

Created at 2021-04-14 14:31:24

------------

As others have explained, it’s just the hexadecimal number `0xf` followed by the operator `or`. Operators generally don’t need surrounding spaces, unless necessary to avoid ambiguity. In this case, the letter `o` cannot be part of a hexadecimal number, so there is no ambiguity. See the [section on whitespace](https://docs.python.org/3/reference/lexical_analysis.html#whitespace-between-tokens) in the Python language reference.
The rest of the line is not evaluated because of short-circuit evaluation, although it is parsed and compiled, of course.
Using that same “trick” you can write similarly obfuscated Python code that doesn’t throw exceptions, for example:
```
>>> 0xbin b'in'
False
>>> 0xbis 1000
False
>>> 0b1and 0b1is 0b00
False
>>> 0o1if 0b1else Oy1then
1
```



------------
    
    