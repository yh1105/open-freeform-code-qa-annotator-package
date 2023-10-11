
# Post \#65229307 [Link](https://stackoverflow.com/questions/65229307/)

## ModuleNotFoundError: No module named 'statsmodels.miscmodels.ordinal_model'

**Vote**: 4 (532/702) **Views**: 4751 (529/702) 

**Internal ID** \#1-3-307

Created at 2020-12-10 06:04:25

Tags: `python` `statsmodels`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I want to build a Ordered Probit model using statsmodel package in python. Used the following code to import:
`from statsmodels.miscmodels.ordinal_model import OrderedModel`
but getting a following error:

I have updated the package as well but the error persists.


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-04-08 14:37:40

------------

I know it is a pretty old discussion board, but I hope my post can be helpful.
I recently ran into the same issue. and solved by doing the following:

1. pip3 install git+https://github.com/statsmodels/statsmodels. Just like @AudiR8 mentioned. However, if you are using an IDE with python version 3.0 +, use pip3 is better.
2. Make sure the package is installed in the correct directory then turn off the IDE.
3. Reopen it and it should be working.


Hope it can be helpful!


------------
    
    
## Answer \#1

 Vote: 4

Created at 2020-12-21 11:40:57

------------

well，u can install this package in this way：
> pip install git+https://github.com/statsmodels/statsmodels


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-27 17:48:05

------------

`pip install --upgrade --no-deps statsmodels` worked for me.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-11-21 08:40:08

------------

An option that helped me was to restore my console's settings to default and then it worked. My IDE in particular was Spyder


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-09-07 16:51:08

------------

You can try use this instead `from statsmodels.discrete.discrete_model import OrderedModel`


------------
    
    