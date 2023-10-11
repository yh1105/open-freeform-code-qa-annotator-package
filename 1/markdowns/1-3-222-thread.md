
# Post \#67280879 [Link](https://stackoverflow.com/questions/67280879/)

## Number of ways to form a string of length n using k characters such that at most two adjacent characters can be same

**Vote**: 2 (631/702) **Views**: 2950 (572/702) 

**Internal ID** \#1-3-222

Created at 2021-04-27 09:58:28

Tags: `python` `algorithm` `dynamic-programming` `combinatorics`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Hi please do let me know what is wrong with my recurrence relation.
My Logic:
The number of ways to allot the first `element=k` and number of ways such that the `next (n-1)` elements are different `(k-1)*f(n-2)`
and number of ways to allot two same elements: `k + (k-1)*f(n-1)`
Hence the relation:
`no. of ways = ∑(k + (k-1)*f(n-1) + k + (k-1)*f(n-1))`
I can see some mistakes like the inclusion of duplicates, however, I am unable to figure out the relation.
You can also find the code below:
```
def count_num_ways(n, k):
        dp = [0 for i in range(n+1)]

        dp[0] = 0
        dp[1] = k

        for i in range(2, n+1):
            dp[i] =  sum(
                [
                     k+( k-1 )*dp[n-2],
                     k+(k-1)*dp[n-1]-k
                ]
            )

        print(dp)
```

Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-04-28 21:53:16

------------

 character position in my explanation begins at 1, so the first character is at position 1, the second character at position 2 and so on.
Let's define:
- `valid string`: string formed using `k` characters such that at most two adjacent characters can be the same.- `f(n)`: number of `valid string` of length `n` and the last two characters are different.- `g(n)`: number of `valid string` of length `n` and the last two characters are the same.- `h(n)`: number of `valid string` of length `n`.
Assume you have already calculated the value of `f(m)` and `g(m)` for all `m <= n`: let's find `f(n+1)` and `g(n+1)`.
`f(n+1)` number of `valid string` of length `n+1` and the last two characters are different. Based on every `valid string` of length `n` you have found:
- if last two characters are different, you have `k-1` ways to place a character at position `n+1` and form a `valid string` of length `n+1` where last two characters are different.- if last two characters are the same, you have `k-1` ways to place a character at position `n+1` and form a `valid string` of length `n+1` where last two characters are different.
These two options are clearly mutually excluse because last two characters of every `valid string` of length `n` can't be both equals and different. Also, these two options are enough to help us to correctly calculate all `valid string` of length `n+1` where last two characters are different, because every `valid string` of length `n+1` where last two characters are different has the characters at position `n-1` and `n` either different or the same (both options above).
So we can finally say: `f(n+1) = (k-1) * f(n) + (k-1) * g(n)`
                                                    | first option |    |second option|
`g(n+1)` number of `valid string` of length `n+1` and the last two characters are the same. Based on every `valid string` of length `n` you have found:
- if last two characters are different, you have just 1 way to place a character at position `n+1` and form a `valid string` of length `n+1` where last two characters are the same.- if last two characters are the same, you have 0 ways to place a character at position `n+1` and form a `valid string` of length `n+1` where last two characters are the same, because a `valid string` has at most two equals adjacent characters. So if characters at position `n+1` and `n` are the same then last 3 characters are the same.
Once again, these two options are clearly mutually excluse because last two characters of every `valid string` of length `n` can't be both equals and different. Also, these two options are enough to help us to correctly calculate all `valid string` of length `n+1` where last two characters are the same because every `valid string` of length `n+1` where last two characters are the same has the characters at position `n-1` and `n` either different or the same (both options above).
So we can finally say: `g(n+1) = f(n)`
                                                 | first option |
Now it's straightforward to see that: `h(n) = f(n) + g(n)` because last two characters of a `valid string` of length `n` are either equals or different.
Given that equation and replacing `g(n)` with it's value, we have: `h(n) = f(n) + f(n-1)`.
Also, given both `f(n)` and `g(n)` just need 1 previous value to be correctly calculated, we have to calculate `f(1)` and `g(1)`` beforehand.
It's easy to see that:
- `f(1) = k`- `g(1) = 0`

 all solutions are written in python code.
You have 3 ways to do this, both of them calculate `h(n)` with `O(n)` time complexity and the third calculates `h(n)` with `O(log n)` time complexity. Let's check them!

Time Complexity: `O(n)`
For solution #1 let's ask for values and assume previous values are already calculated (for this we need recursion).
```
def f(n, k):
    return k if n == 1 else (k - 1) * (f(n - 1, k) + g(n - 1, k))

def g(n, k):
    return 0 if n == 1 else f(n - 1, k)

def h(n, k):
    return f(n, k) + g(n, k)
```


Time Complexity: `O(n)`
For solution #2 let's calculate the next value of those formulas based on previous already calculated values.
```
def h(n, k):
    fn, gn = k, 0
    for _ in range(n):
        hn = fn + gn
        fn, gn = (k - 1) * (fn + gn), fn
    return hn
```

[Matrix Exponentiation](https://www.geeksforgeeks.org/matrix-exponentiation/)[Binary Exponentiation](https://en.wikipedia.org/wiki/Exponentiation_by_squaring)
Time Complexity: `O(log n)`
Given `h(n) = f(n) + g(n)` and `g(n) = f(n-1)` then `h(n) = f(n) + f(n-1)`.
Also, given `f(n) = (k-1) * f(n-1) + (k-1) * g(n-1)` and `g(n) = f(n-1)`
then `f(n) = (k-1) * f(n-1) + (k-1) * f(n-2)`.
So with matrix and vectors below and assuming `f(0) = 0`, we can correctly calculate `h(n)` as follows:
```
| k - 1      k - 1 |^(n-1)   *   | f(n-1) |   =   | f(n)   |
|   1          0   |             | f(n-2) |       | f(n-1) |
```

Code:
```
def h(n, k):
    def _matrix_multiplication(matrix_a, matrix_b):
        result = [ [ 0 ] * len(matrix_b[ 0 ]) for _ in range(len(matrix_a)) ]
        for i in range(len(result)):
            for j in range(len(result[ 0 ])):
                for k in range(len(matrix_a[ 0 ])):
                    result[ i ][ j ] += matrix_a[ i ][ k ] * matrix_b[ k ][ j ]
        return result

    def _binary_exponentiation(matrix, n):
        result = [
            [ 1, 0 ],
            [ 0, 1 ]
        ]
        while n != 0:
            if n % 2 == 1:
                result = _matrix_multiplication(result, matrix)
            n //= 2
            matrix = _matrix_multiplication(matrix, matrix)
        return result

    matrix = [
        [ k - 1, k - 1 ],
        [ 1, 0 ]
    ]

    matrix = _binary_exponentiation(matrix, n - 1)
    vector1 = [ [ k ], [ 0 ] ]
    vector2 = _matrix_multiplication(matrix, vector1)
    return sum(row[ 0 ] for row in vector2)
```

I hope any of these 3 solutions help you!


------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-04-27 15:12:49

------------

Let `f(n)` equals the number of ways to form n elements while the last two characters are ,
```
---+-+-+-+-+---+-+
...|a|a| | |...| |
---+-+-+-+-+---+-+
        \---v---/
            n
f(n) = number of ways to fill these n elements.
```

and let `g(n)` equals the number of ways to form n elements while the last two characters are .
```
---+-+-+-+-+---+-+
...|a|b| | |...| |
---+-+-+-+-+---+-+
        \---v---/
            n
g(n) = number of ways to fill these n elements.
```

As `f(n)`, since the last two characters are the same, we need to choose a different character which is `(k-1)` and the last two characters will be different which is `g(n-1)`, the result is:
```
f(n) = (k-1)*g(n-1)
```

As `g(n)`, since the last two characters are different, we can either choose the same character or a different one. For the first case, the same character is the only choice: `1`, and the last two characters are the same: `f(n-1)`. For the second case, different character options: `(k-1)` and the different function: `g(n-1)`, the result is:
```
g(n) = 1*f(n-1) + (k-1)*g(n-1)
```

For the first element, we choose a character `k`, and the answer is
```
k*g(n-1)
```


### Ps:


You can actually substitute `f(n-1)` in the second equation, but this is more intuitive I think.

### Code


Here is an example Python code
```
def f(n):
    if n == 0:
        return 1
    return (k-1)*g(n-1)


def g(n):
    if n == 0:
        return 1
    return f(n-1) + (k-1)*g(n-1)


n, k = 4, 2

print(k*g(n-1))
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-04-28 20:31:52

------------

Based on your question, I draw `2` conclusions:

1. The same character can be used any number of times when forming a string.
2. k > 1 because otherwise the solution will not exist for n > 2.


In such case, it's a typical combinatorics problem and you can count the number of combinations with a simple math formula. You need to consider `2` cases - all adjacent characters are different, and exactly 2 adjacent characters are the same.

# Explanation



## Case A - all adjacent characters are different


There are `k` ways to pick the first character in the string. For the next characters, there are always `k - 1` ways (they must be different!). Hence, the total number of combinations is k(k-1).

## Case B - 2 adjacent characters are same


Let's say that `2` identical characters are first and second characters of the string. There are `k` ways to create such pair because both elements are equal! Then, for every other position in the string, there are `k - 1` ways to pick a character.
What, if a pair of identical elements is in the middle or at the end? Well, the number of combinations will remain the same - it's always `k` ways to pick the first element in the string and `k - 1` ways to pick the other elements. If `2` positions have to be occupied by equal characters, then there are, as before, `k - 1` ways to set them. Thereof, there are k(k-1) for a fixed pair position.
Since the number of such positions is `n-1`, the number of combinations is (n-1)k(k-1)

## Result


So, the overall number of combinations is k(k-1) + (n-1)k(k-1)


------------
    
    