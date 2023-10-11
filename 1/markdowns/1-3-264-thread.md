
# Post \#45554008 [Link](https://stackoverflow.com/questions/45554008/)

## Error in Python script "Expected 2D array, got 1D array instead:"?

**Vote**: 115 (47/702) **Views**: 367442 (14/702) 

**Internal ID** \#1-3-264

Created at 2017-08-07 19:02:14

Tags: `python` `python-3.x` `machine-learning` `predict`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm following [this tutorial](https://pythonprogramming.net/linear-svc-example-scikit-learn-svm-python) to make this ML prediction:

```
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import style

style.use("ggplot")
from sklearn import svm

x = [1, 5, 1.5, 8, 1, 9]
y = [2, 8, 1.8, 8, 0.6, 11]

plt.scatter(x,y)
plt.show()

X = np.array([[1,2],
             [5,8],
             [1.5,1.8],
             [8,8],
             [1,0.6],
             [9,11]])

y = [0,1,0,1,0,1]
X.reshape(1, -1)

clf = svm.SVC(kernel='linear', C = 1.0)
clf.fit(X,y)

print(clf.predict([0.58,0.76]))
```


I'm using Python 3.6 and I get error "Expected 2D array, got 1D array instead:"
I think the script is for older versions, but I don't know how to convert it to the 3.6 version.

Already try with the:

```
X.reshape(1, -1)
```



----------
        
## Answer \#0

**Accepted** Vote: 211

Created at 2017-08-07 19:12:51

------------

You are just supposed to provide the `predict` method with the same 2D array, but with one value that you want to process (or more). In short, you can just replace

```
[0.58,0.76]
```


With

```
[[0.58,0.76]]
```


And it should work.

EDIT: This answer became popular so I thought I'd add a little more explanation about ML. The short version: we can only use `predict` on data that is of the same dimensionality as the training data (`X`) was. 

In the example in question, we give the computer a bunch of rows in `X` (with 2 values each) and we show it the correct responses in `y`. When we want to `predict` using new values, our program expects the same - a  of rows. Even if we want to do it to just one row (with two values), that row has to be part of another array. 


------------
    
    
## Answer \#1

 Vote: 23

Created at 2017-08-07 19:17:56

------------

The problem is occurring when you run prediction on the array `[0.58,0.76]`. Fix the problem by reshaping it before you call `predict()`:

```
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import style

style.use("ggplot")
from sklearn import svm

x = [1, 5, 1.5, 8, 1, 9]
y = [2, 8, 1.8, 8, 0.6, 11]

plt.scatter(x,y)
plt.show()

X = np.array([[1,2],
             [5,8],
             [1.5,1.8],
             [8,8],
             [1,0.6],
             [9,11]])

y = [0,1,0,1,0,1]

clf = svm.SVC(kernel='linear', C = 1.0)
clf.fit(X,y)

test = np.array([0.58, 0.76])
print test       # Produces: [ 0.58  0.76]
print test.shape # Produces: (2,) meaning 2 rows, 1 col

test = test.reshape(1, -1)
print test       # Produces: [[ 0.58  0.76]]
print test.shape # Produces (1, 2) meaning 1 row, 2 cols

print(clf.predict(test)) # Produces [0], as expected
```



------------
    
    
## Answer \#2

 Vote: 12

Created at 2019-04-28 10:29:10

------------

I use the below approach.

```
reg = linear_model.LinearRegression()
reg.fit(df[['year']],df.income)

reg.predict([[2136]])
```



------------
    
    
## Answer \#3

 Vote: 7

Created at 2018-06-04 17:06:52

------------

I faced the same issue except that the data type of the instance I wanted to predict was a `panda.Series` object.

Well I just needed to predict one input instance. I took it from a slice of my data.

```
df = pd.DataFrame(list(BiogasPlant.objects.all()))
test = df.iloc[-1:]       # sliced it here
```


In this case, you'll need to convert it into a 1-D array  and then `reshape` it.

```
test2d = test.values.reshape(1,-1)
```


From the [docs](http://pandas.pydata.org/pandas-docs/version/0.22/generated/pandas.Series.values.html), `values` will convert Series into a numpy array.


------------
    
    
## Answer \#4

 Vote: 3

Created at 2019-01-11 03:48:28

------------

I faced the same problem. You just have to make it an array and moreover you have to put double squared brackets to make it a single element of the 2D array as first bracket initializes the array and the second makes it an element of that array. 

So simply replace the last statement by:

```
print(clf.predict(np.array[[0.58,0.76]]))
```



------------
    
    
## Answer \#5

 Vote: 3

Created at 2019-10-08 08:35:43

------------

Just insert the argument between a double square bracket:
```
regressor.predict([[values]])
```

that worked for me


------------
    
    
## Answer \#6

 Vote: 1

Created at 2019-10-05 19:45:11

------------

I was facing the same issue earlier but I have somehow found the solution,
You can try `reg.predict([[3300]])`.  

The API used to allow scalar value but now you need to give a 2D array.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2019-04-23 08:48:01

------------

With one feature my Dataframe list converts to a Series. I had to convert it back to a Dataframe list and it worked.

```
if type(X) is Series:
    X = X.to_frame()
```



------------
    
    
## Answer \#8

 Vote: 0

Created at 2021-06-06 16:59:57

------------


For example:
If initially your `x = [8,9,12,7,5]`
change it to `x = [ [8,9,12,7,5] ]`.
That should fix the dimension issue


------------
    
    
## Answer \#9

 Vote: 0

Created at 2021-12-03 14:26:27

------------

You can do it like this:
np.array(x)[:, None]


------------
    
    
## Answer \#10

 Vote: -1

Created at 2018-05-26 15:07:57

------------

The X and Y matrix of Independent Variable and Dependent Variable respectively to DataFrame from int64 Type so that it gets converted from 1D array to 2D array.. 
i.e X=pd.DataFrame(X) and Y=pd.dataFrame(Y) where pd is of pandas class in python. and thus feature scaling in-turn doesn't lead to any error!


------------
    
    