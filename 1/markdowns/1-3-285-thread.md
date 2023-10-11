
# Post \#49147774 [Link](https://stackoverflow.com/questions/49147774/)

## What is "random-state" in sklearn.model_selection.train_test_split example?

**Vote**: 33 (185/702) **Views**: 135990 (58/702) 

**Internal ID** \#1-3-285

Created at 2018-03-07 09:00:07

Tags: `python` `numpy` `machine-learning` `random` `scikit-learn`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Can someone explain me what `random_state` means in below example?
```
import numpy as np
from sklearn.model_selection import train_test_split
X, y = np.arange(10).reshape((5, 2)), range(5)


X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.33, random_state=42)
```

Why is it hard coded to 42?


----------
        
## Answer \#0

**Accepted** Vote: 91

Created at 2018-03-07 09:04:27

------------

Isn't that obvious? 42 is the [Answer to the Ultimate Question of Life, the Universe, and Everything](https://en.wikipedia.org/wiki/42_(number)#Popular_culture).

On a serious note, `random_state` simply sets a seed to the random generator, so that your train-test splits are always deterministic. If you don't set a seed, it is different each time.

[Relevant documentation](http://scikit-learn.org/dev/modules/generated/sklearn.model_selection.train_test_split.html#sklearn.model_selection.train_test_split):

> `random_state` : `int``RandomState``None``None`
  If `int`, `random_state` is the seed used by the random
  number generator; If `RandomState` instance, `random_state` is the random
  number generator; If `None`, the random number generator is the
  `RandomState` instance used by `np.random`.


------------
    
    
## Answer \#1

 Vote: 18

Created at 2020-01-22 12:09:31

------------

If you don't specify the random_state in the code, then every time you run(execute) your code a new random value is generated and the train and test datasets would have different values each time.

However, if a fixed value is assigned like random_state = 0 or 1 or 42 or any other integer then no matter how many times you execute your code the result would be the same .i.e, same values in train and test datasets.


------------
    
    
## Answer \#2

 Vote: 10

Created at 2018-03-07 09:05:37

------------

Random state ensures that the splits that you generate are reproducible. Scikit-learn uses random permutations to generate the splits. The random state that you provide is used as a seed to the random number generator. This ensures that the random numbers are generated in the same order.


------------
    
    
## Answer \#3

 Vote: 6

Created at 2020-03-29 17:12:32

------------

When the Random_state is not defined in the code for every run train data will change and accuracy might change for every run.
When the Random_state = " constant integer" is defined then train data will be constant For every run so that it will make easy to debug.


------------
    
    
## Answer \#4

 Vote: 2

Created at 2020-12-01 08:23:28

------------

The random state is simply the lot number of the set generated randomly in any operation. We can specify this lot number whenever we want the same set again.


------------
    
    