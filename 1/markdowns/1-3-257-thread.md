
# Post \#70392403 [Link](https://stackoverflow.com/questions/70392403/)

## Dividing an even number into N parts each part being a multiple of 2

**Vote**: 7 (449/702) **Views**: 1481 (626/702) 

**Internal ID** \#1-3-257

Created at 2021-12-17 11:22:11

Tags: `python` `algorithm` `math` `numbers`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Let's assume I have the number `100` which I need to divide into N parts each of which shouldn't exceed 30 initially. So the initial grouping would be (30,30,30). The remainder (which is 10) is to be distributed among these three groups by adding 2 to each group in succession, thus ensuring that each group is a multiple of 2. The desired output should therefore look like (34,34,32).
Note: The original number is always even.
I tried solving this in Python and this is what I came up with. Clearly it's not working in the way I thought it would. It distributes the remainder by adding 1 (and not 2, as desired) iteratively to each group.
```
num = 100
parts = num//30  #Number of parts into which 'num' is to be divided

def split(a, b):
  result = ([a//b + 1] * (a%b) + [a//b] * (b - a%b))
  return(result)

print(split(num, parts))
```

Output:
```
[34, 33, 33]
```

Desired output:
```
[34, 34, 32]
```



----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-12-17 11:48:08

------------


## Simplified problem: forget about multiples of 2


First, let's simplify your problem for a second. Forget about the multiples of 2. Imagine you want to split a non-necessarily-even number `n` into `k` non-necessarily-even parts.
Obviously the most balanced solution is to have some parts be `n // k`, and some parts be `n // k + 1`.
How many of which? Let's call `r` the number of parts with `n // k + 1`. Then there are `k - r` parts with `n // k`, and all the parts sum up to:
```
(n // k) * (k - r) + (n // k + 1) * r
== (n // k) * (k - r) + (n // k) * r + r
== (n // k) * (k - r + r) + r
== (n // k) * k + r
```

But the parts should sum up to `n`, so we need to find `r` such that:
```
n == (n // k) * k + r
```

Happily, you might recognise Euclidean division here, with `n // k` being the quotient and `r` being the remainder.
This gives us our `split` function:
```
def split(n, k):
    d,r = divmod(n, k)
    return [d+1]*r + [d]*(k-r)
```

Testing:
```
print( split(50, 3) )
# [17, 17, 16]
```


## Splitting into multiples of 2


Now back to your `split_even` problem. Now that we have the generic function `split`, a simple way to solve `split_even` is to use `split`:
```
def split_even(n, k):
    return [2 * x for x in split(n // 2, k)]
```

Testing:
```
print( split_even(100, 3) )
# [34, 34, 32]
```


## Generalisation: multiples of m


It's trivial to do the same thing with multiples of a number `m` other than 2:
```
def split_multiples(n, k, m=2):
    return [m * x for x in split(n // m, k)]
```

Testing:
```
print( split_multiples(102, 4, 3) )
# [27, 27, 24, 24]
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-12-17 12:06:02

------------

This solution is not very clear and easy to follow but it does not need any loops.
Full code:
```
def split(a,b):
     lower = (a//b//2) * 2
     num = a % (b*2) // 2
     return [lower + 2] * num + [lower] * (b - num)
```

Explanation:
- `value // parts``(x // 2) * 2`- `a`- `lower + 2`


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-12-17 11:53:04

------------

My approach here is to create three arrays and sum them, the first two are simple, but the last is a little more complex to follow - it's just repping 2 (by) as many times as is can given the remainder, then repping 0s.
```
# Part 1
np.repeat(first, x//first) 
# Part 2
np.repeat(by, x//first)
# Part 3
np.repeat([by, 0], [(x//first) - ((x - (x//first*first)) // by % by), (x - (x//first*first)) // by % by])
```

Wrapped into a function:
```
def split(x, first, by):
  return(np.repeat(first, x//first) + np.repeat(by, x//first) + np.repeat([by, 0], [(x//first) - ((x - (x//first*first)) // by % by), (x - (x//first*first)) // by % by]))

split(100, 30, 2)
```



------------
    
    