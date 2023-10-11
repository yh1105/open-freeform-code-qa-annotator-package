
# Post \#58386953 [Link](https://stackoverflow.com/questions/58386953/)

## pip3 "TypeError: 'module' object is not callable" after update

**Vote**: 47 (131/702) **Views**: 19695 (307/702) 

**Internal ID** \#1-3-262

Created at 2019-10-15 03:45:40

Tags: `python` `ubuntu` `pip`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am new in Python, I wanna install Jupyter Notebook in my console I enter the following:

```
pip3 install --upgrade pip
```


after that I have a error to use pip3 install other library, the console print: 

```
File "/usr/bin/pip3", line 11, in <module>
    sys.exit(main())
TypeError: 'module' object is not callable
```


I don't know what I have to do. 

I use `sudo autoremove python3-pip` after that I use `sudo apt install python3-pip`


----------
        
## Answer \#0

**Accepted** Vote: 66

Created at 2019-10-15 07:14:53

------------

From the [link](https://github.com/pypa/pip/issues/7205#issuecomment-541926212) by Bram, I just ran `python3 -m pip uninstall pip`, and it started to work again.


------------
    
    
## Answer \#1

 Vote: 6

Created at 2019-12-06 13:08:11

------------

The solution which worked for my situation is simply editing the pip3.8 file in the ubuntu environment.

Method1:

```
#!/path/to/.venv/bin/python3
# -*- coding: utf-8 -*-
import re
import sys

from pip._internal.main import main  # <--- look at this import statement! 

if __name__ == '__main__':
     sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
     sys.exit(main())
```


method2:

The main function has to be imported or we can simply replace line 

```
sys.exit(main())
```


As

```
sys.exit(main.main())
```



------------
    
    
## Answer \#2

 Vote: 6

Created at 2019-12-19 09:15:51

------------

Use this

> python -m pip install --upgrade --user [name_of_your_package]


------------
    
    
## Answer \#3

 Vote: 4

Created at 2019-10-15 06:46:06

------------

As seen [here](https://github.com/pypa/pip/issues/7205#issuecomment-541926212), you should be able to solve this by running the module from Python directly, i.e.

```
python -m pip install --upgrade pip
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-01-21 05:41:37

------------

In Windows edit C:\ProgramData\Anaconda3\Scripts\pip-script.py and replace 

```
# -*- coding: utf-8 -*-
import re
import sys
from pip._internal import main

if __name__ == '__main__':
    sys.argv[0] = re.sub(r'(-script\.pyw?|\.exe)?$', '', sys.argv[0])
    sys.exit(main())
```


> Replace the last line with sys.exit(main.main()). 


------------
    
    