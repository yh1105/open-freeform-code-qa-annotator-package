
# Post \#54915381 [Link](https://stackoverflow.com/questions/54915381/)

## Will PIP work for python 2.7 after its End of Life on 1st Jan 2020

**Vote**: 26 (219/702) **Views**: 41673 (191/702) 

**Internal ID** \#1-3-256

Created at 2019-02-27 22:16:58

Tags: `python` `python-3.x` `python-2.7` `pip`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Python 2.7 is reaching its end of life on 1st Jan, 2020 as mentioned by 

[https://legacy.python.org/dev/peps/pep-0373/](https://legacy.python.org/dev/peps/pep-0373/)

[https://pythonclock.org/](https://pythonclock.org/)

Will current `pip` keep on working for python 2.7 after that date? It is already showing the msg for deprecation of python 2.7. Will we be able to run
`pip install abc==1.2.3` after that date?

We do understand that after EOL, no new fixes/support will be done for 2.7, so that is not the concern here. 

The question stems from the desire to keep running on python 2.7 even after EOL.


----------
        
## Answer \#0

**Accepted** Vote: 21

Created at 2019-02-27 22:57:16

------------

You need to make sure you have the right version of `pip` for Python 2.7:
```
pip install --upgrade "pip < 21.0"
```

That versions will be working for some time. Sooner or later they fail because PyPI host changes SSL configuration to one that Python 2.7 doesn't handle. But I'm sure we have a few years before that.
A few additional version limits for important tools to retain compatibility with Python 2.7:
```
pip install --upgrade "setuptools < 45" "Sphinx < 2.0" "pytest < 5.0"
```

If you use other tools follow their announcements thoroughly to catch them stopping supporting 2.7.


------------
    
    
## Answer \#1

 Vote: 14

Created at 2019-08-27 03:06:21

------------

Yes. Python 2 support will live in a "community maintained" state though. Folks who care about it, will have to step up and fix issues -- pip maintainers will likely not work on resolving Python 2 issues. We've documented the exact expectations at:

[https://pip.pypa.io/en/stable/development/release-process/#python-2-support](https://pip.pypa.io/en/stable/development/release-process/#python-2-support)

Note that pip maintainers are volunteers and I, personally, am not interested in doing free work for corporations / organizations which are stuck on an EOL'ed Python even after having a decade to switch.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-05-27 05:55:43

------------

Yes, Pip for python2.7 is part of Ubuntu 18, which EOLs in April 2028.
Ubuntu is really good about maintaining their distros.
Then, by April 2028, it is likely that a new Python2.7-compatible language will be developed and maintained, which will support `pip` or something similar. Some kind of compiler hook will be added to Python3 to change the `str` back to byte strings and make the parenthesis on `print` optional. Or maybe we'll switch to `Cython` or some other implementation of Python2.7. But don't worry about that for a few years (unless you have a lot of free time).


------------
    
    