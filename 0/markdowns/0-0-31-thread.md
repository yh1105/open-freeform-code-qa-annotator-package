
# Post \#62929428 [Link](https://stackoverflow.com/questions/62929428/)

## Opposite of nullish coalescing operator

**Vote**: 42 (147/702) **Views**: 7498 (473/702) 

**Internal ID** \#0-0-31

Created at 2020-07-16 07:15:09

Tags: `javascript` `typescript` `nullish-coalescing`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Nullish coalescing operator allows assigning a variable if it's not `null` or `undefined`, or an expression otherwise.
```
a = b ?? other
```

It is an improvement over previously used `||` because `||` will also assign other if `b` is empty string or other falsy, but not nullish value.
However, sometimes, we also use `&&` for value assignment, for example
```
a = b && func(b)
```

where we only want to do `func` on `b` if it's not nullish, otherwise assign the nullish `b`.
Of course, `&&` checks for falsiness, not nullishness. Is there a nullish version of `&&`?


----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2020-07-16 07:51:21

------------

To my knowledge, there is no such operator and also no [proposal](https://github.com/tc39/proposals) to add one. Instead you can rely on the [standard way to check for nullish values](https://tc39.es/ecma262/#sec-abstract-equality-comparison): `b == null`
```
a = b == null ? b : func(b)
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2021-02-18 20:48:38

------------

This will not answer the question since it was already answered by @str, I'm just posting this here because I don't have enough rep to comment on @Dalou's answer and don't want people to trip on that answer.
```
a = (b ?? false) && other
```

Is not the opposite of `??`, since `a` will take the value of `b` if `b` is a falsy value other than undefined/null, like `''` or `0` for example.
The opposite of `??` should set `a` to the value of `other` even if `b` is `''` or `0`.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-06-21 10:56:31

------------

ANSWER:
```
let a = 9;

if( a!==null || a!==undefined ){ /* actions */ };

if( (a??null) !== null ){ /* actions */ }
```

PROPOSAL:
```
const oo = console.log;
let a; // undefined, null

// Syntax proposal: !?
a !? oo('NOT Nullish coalescing operator');

// Logical NOT Nullish assignment:
let a = 7;
a !?= 5;
oo(a); // 5

let b, c = null;
b !?= 3;
oo(b); // undefined
c !?= 8;
oo(c); // null
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-08-27 23:04:32

------------

I don't like it but here's my solution:
```
output = typeof input === 'boolean' && "other"
```

Truth table:
```
input     -> output
--------------------
null      -> false
undefined -> false
true      -> "other"
false     -> "other"
```



------------
    
    