
# Post \#71753428 [Link](https://stackoverflow.com/questions/71753428/)

## How to get SHAP values for each class on a multiclass classification problem in python

**Vote**: 4 (532/702) **Views**: 6521 (491/702) 

**Internal ID** \#1-3-290

Created at 2022-04-05 14:21:03

Tags: `python` `python-3.x` `machine-learning` `xgboost` `shap`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have the following dataframe:
```
import pandas as pd
import random

import xgboost
import shap

foo = pd.DataFrame({'id':[1,2,3,4,5,6,7,8,9,10],
                   'var1':random.sample(range(1, 100), 10),
                   'var2':random.sample(range(1, 100), 10),
                   'var3':random.sample(range(1, 100), 10),
                   'class': ['a','a','a','a','a','b','b','c','c','c']})
```

For which I want to run a classification algorithm in order to predict the 3 `class`es
So I split my dataset into train and test and I run an xgboost
```
cl_cols = foo.filter(regex='var').columns
X_train, X_test, y_train, y_test = train_test_split(foo[cl_cols],
                                                        foo[['class']],
                                                        test_size=0.33, random_state=42)


model = xgboost.XGBClassifier(objective="binary:logistic")
model.fit(X_train, y_train)
```

Now I would like to get the mean SHAP values , instead of the mean from the  SHAP values generated from this code:
```
shap_values = shap.TreeExplainer(model).shap_values(X_test)
shap.summary_plot(shap_values, X_test)
```

[](https://i.stack.imgur.com/DsAik.png)
Also, the plot labels the `class` as 0,1,2. How can I know to which `class` from the original do the 0,1 & 2 correspond ?
Because this code:
```
shap.summary_plot(shap_values, X_test,
                 class_names= ['a', 'b', 'c'])
```

gives
[](https://i.stack.imgur.com/SR1ju.png)
and this code:
```
shap.summary_plot(shap_values, X_test,
                 class_names= ['b', 'c', 'a'])
```

gives
[](https://i.stack.imgur.com/Z1s0M.png)
So I am not sure about the legend anymore.
Any ideas ?


----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2022-04-11 09:38:50

------------

By doing some research and with the help of [this post](https://towardsdatascience.com/explain-your-model-with-the-shap-values-bc36aac4de3d) and @Alessandro Nesti 's answer, here is my solution:
```
foo = pd.DataFrame({'id':[1,2,3,4,5,6,7,8,9,10],
                   'var1':random.sample(range(1, 100), 10),
                   'var2':random.sample(range(1, 100), 10),
                   'var3':random.sample(range(1, 100), 10),
                   'class': ['a','a','a','a','a','b','b','c','c','c']})

cl_cols = foo.filter(regex='var').columns
X_train, X_test, y_train, y_test = train_test_split(foo[cl_cols],
                                                        foo[['class']],
                                                        test_size=0.33, random_state=42)


model = xgboost.XGBClassifier(objective="multi:softmax")
model.fit(X_train, y_train)

def get_ABS_SHAP(df_shap,df):
    #import matplotlib as plt
    # Make a copy of the input data
    shap_v = pd.DataFrame(df_shap)
    feature_list = df.columns
    shap_v.columns = feature_list
    df_v = df.copy().reset_index().drop('index',axis=1)
    
    # Determine the correlation in order to plot with different colors
    corr_list = list()
    for i in feature_list:
        b = np.corrcoef(shap_v[i],df_v[i])[1][0]
        corr_list.append(b)
    corr_df = pd.concat([pd.Series(feature_list),pd.Series(corr_list)],axis=1).fillna(0)
 
    # Make a data frame. Column 1 is the feature, and Column 2 is the correlation coefficient
    corr_df.columns  = ['Variable','Corr']
    corr_df['Sign'] = np.where(corr_df['Corr']>0,'red','blue')
    
    shap_abs = np.abs(shap_v)
    k=pd.DataFrame(shap_abs.mean()).reset_index()
    k.columns = ['Variable','SHAP_abs']
    k2 = k.merge(corr_df,left_on = 'Variable',right_on='Variable',how='inner')
    k2 = k2.sort_values(by='SHAP_abs',ascending = True)
    
    k2_f = k2[['Variable', 'SHAP_abs', 'Corr']]
    k2_f['SHAP_abs'] = k2_f['SHAP_abs'] * np.sign(k2_f['Corr'])
    k2_f.drop(columns='Corr', inplace=True)
    k2_f.rename(columns={'SHAP_abs': 'SHAP'}, inplace=True)
    
    return k2_f

foo_all = pd.DataFrame()

for k,v in list(enumerate(model.classes_)):

    foo = get_ABS_SHAP(shap_values[k], X_test)
    foo['class'] = v
    foo_all = pd.concat([foo_all,foo])

import plotly_express as px
px.bar(foo_all,x='SHAP', y='Variable', color='class')
```

which results in [](https://i.stack.imgur.com/h1PLo.png)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2022-04-08 16:37:12

------------

I had the same question, perhaps this issue can help: [https://github.com/slundberg/shap/issues/764](https://github.com/slundberg/shap/issues/764)
I haven't tested it yet, but it seems the order should be the same as the order you would have when calling `model.predict_proba()`. In the link above it is suggested to use the `class_names=model.classes_` option of the summary plot.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-04 20:33:49

------------

This is an updated code of @[quant](https://stackoverflow.com/users/5868293/quant)'s code:
```
import pandas as pd
import random

import numpy as np

import xgboost
import shap

from sklearn.model_selection import train_test_split

import plotly_express as px


foo = pd.DataFrame({'id':[1,2,3,4,5,6,7,8,9,10],
                   'var1':random.sample(range(1, 100), 10),
                   'var2':random.sample(range(1, 100), 10),
                   'var3':random.sample(range(1, 100), 10),
                   'class': ['a','a','a','a','a','b','b','c','c','c']})

foo['class'], _ = pd.factorize(foo['class'], sort = True)

cl_cols = foo.filter(regex='var').columns
X_train, X_test, y_train, y_test = train_test_split(foo[cl_cols],
                                                        foo[['class']],
                                                        test_size=0.33, random_state=42)

model = xgboost.XGBClassifier(objective="multi:softmax")
model.fit(X_train, y_train)

shap_values = shap.TreeExplainer(model).shap_values(X_test)







def get_ABS_SHAP(df_shap,df):
    #import matplotlib as plt
    # Make a copy of the input data
    shap_v = pd.DataFrame(df_shap)
    feature_list = df.columns
    shap_v.columns = feature_list
    df_v = df.copy().reset_index().drop('index',axis=1)
    
    # Determine the correlation in order to plot with different colors
    corr_list = list()
    for i in feature_list:
        b = np.corrcoef(shap_v[i],df_v[i])[1][0]
        corr_list.append(b)
    corr_df = pd.concat([pd.Series(feature_list),pd.Series(corr_list)],axis=1).fillna(0)
 
    # Make a data frame. Column 1 is the feature, and Column 2 is the correlation coefficient
    corr_df.columns  = ['Variable','Corr']
    corr_df['Sign'] = np.where(corr_df['Corr']>0,'red','blue')
    
    shap_abs = np.abs(shap_v)
    k=pd.DataFrame(shap_abs.mean()).reset_index()
    k.columns = ['Variable','SHAP_abs']
    k2 = k.merge(corr_df,left_on = 'Variable',right_on='Variable',how='inner')
    k2 = k2.sort_values(by='SHAP_abs',ascending = True)
    
    k2_f = k2[['Variable', 'SHAP_abs', 'Corr']]
    k2_f['SHAP_abs'] = k2_f['SHAP_abs'] * np.sign(k2_f['Corr'])
    k2_f.drop(columns='Corr', inplace=True)
    k2_f.rename(columns={'SHAP_abs': 'SHAP'}, inplace=True)
    
    return k2_f

foo_all = pd.DataFrame()

for k,v in list(enumerate(model.classes_)):

    foo = get_ABS_SHAP(shap_values[k], X_test)
    foo['class'] = v
    foo_all = pd.concat([foo_all,foo])

px.bar(foo_all,x='SHAP', y='Variable', color='class')
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-04-10 07:17:38

------------

SHAP values are returned as a list. You can access the regarding SHAP absolute values via their indices.
For the summary plot of your Class 0, the code would be
```
shap.summary_plot(shap_values[0], X_test)
```



------------
    
    