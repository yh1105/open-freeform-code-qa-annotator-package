
# Post \#54347963 [Link](https://stackoverflow.com/questions/54347963/)

## 'tf' is not defined on load_model() - using lambda

**Vote**: 20 (261/702) **Views**: 42285 (189/702) 

**Internal ID** \#1-3-232

Created at 2019-01-24 13:38:19

Tags: `python` `tensorflow` `lambda` `keras`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have a `Keras` model that I am trying to export and use in a different python code.

Here is my code:

```
from keras.models import Sequential
from keras.layers import Dense, Embedding, LSTM, GRU, Flatten, Dropout, Lambda
from keras.layers.embeddings import Embedding
import tensorflow as tf


EMBEDDING_DIM = 100

model = Sequential()
model.add(Embedding(vocab_size, 300, weights=[embedding_matrix], input_length=max_length, trainable=False))
model.add(Lambda(lambda x: tf.reduce_mean(x, axis=1)))
model.add(Dense(8, input_dim=4, activation='relu'))
model.add(Dense(3, activation='softmax'))
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
model.fit(X_train_pad, y_train, batch_size=128, epochs=25, validation_data=(X_val_pad, y_val), verbose=2)
model.save('my_model.h5')
```



---



In , when I import `my_model.h5` :

```
from keras.models import load_model
from keras.layers import Lambda
import tensorflow as tf


def learning(test_samples):
    model = load_model('my_model.h5')
    #ERROR HERE
    #rest of the code
```


The error is the following:

```
in <lambda>
    model.add(Lambda(lambda x: tf.reduce_mean(x, axis=1)))
NameError: name 'tf' is not defined
```


After research, I got that the fact that `lambda` is the reason for this problem, but I added these references and it didn't help:

```
from keras.models import load_model
from keras.layers import Lambda
import tensorflow as tf
```


What could be the problem?

Thank you


----------
        
## Answer \#0

**Accepted** Vote: 43

Created at 2019-01-24 13:42:08

------------

When loading the model, you need to [explicitly handle custom objects or custom layers](https://keras.io/getting-started/faq/#handling-custom-layers-or-other-custom-objects-in-saved-models) (CTRL+f the docs for ):

```
import tensorflow as tf
import keras
model = keras.models.load_model('my_model.h5', custom_objects={'tf': tf})
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2019-01-24 13:43:40

------------

It happened to me too. You need to import tensorflow inside your lambda function. So you probably want to put the code in a separate function:

```
def reduce_mean(x):
    import tensorflow as tf
    return tf.reduce_mean(x, axis=1)

model = Sequential()
model.add(Embedding(vocab_size, 300, weights=[embedding_matrix], input_length=max_length, trainable=False))
model.add(Lambda(reduce_mean))
model.add(Dense(8, input_dim=4, activation='relu'))
model.add(Dense(3, activation='softmax'))
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
model.fit(X_train_pad, y_train, batch_size=128, epochs=25, validation_data=(X_val_pad, y_val), verbose=2)
model.save('my_model.h5')
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2019-07-16 17:47:07

------------

Besides, when you try to load the model structure from a json file, passing custom objects solves the problem.

```
model = model_from_json(open("model_structure.json", "r").read(), custom_objects={'tf': tf})
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2019-12-22 20:40:28

------------

One weird solution that has worked for me is to import the module used to create the model.  So say you have file you use to create a model and save it.



```
from keras.models import Sequential
from keras.layers import Dense, Embedding, LSTM, GRU, Flatten, Dropout, Lambda
from keras.layers.embeddings import Embedding
import tensorflow as tf


EMBEDDING_DIM = 100

model = Sequential()
model.add(Embedding(vocab_size, 300, weights=[embedding_matrix], input_length=max_length, trainable=False))
model.add(Lambda(lambda x: tf.reduce_mean(x, axis=1)))
...
model.fit(X_train_pad, y_train, batch_size=128, epochs=25, validation_data=(X_val_pad, y_val), verbose=2)
model.save('my_model.h5')
```


If you load the model in another file , you may be able to get around the error via  of the first module.

```
from keras.models import load_model
from keras.layers import Lambda
import tensorflow as tf

import make_model

def learning(test_samples):
    model = load_model('my_model.h5')
```


This worked for me since the error read 

```
UserWarning: make_model is not loaded, but a Lambda layer uses it. It may cause errors.
...
NameError: name 'tf' is not defined
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-09-02 15:42:44

------------

Happened to me as well - however problem was that the code was refactored and the `Lambda` layer was replaced by something else. In this case, you can always rollback to the point where it worked.
The simple workaround in case you are not able to restore the previous solution is adding: `custom_objects={'tf': tf}` to `restore_model` call


------------
    
    