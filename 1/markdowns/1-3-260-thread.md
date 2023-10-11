
# Post \#63394364 [Link](https://stackoverflow.com/questions/63394364/)

## ModuleNotFoundError: No module named 'bs4' [BeautifulSoup]

**Vote**: 2 (631/702) **Views**: 7370 (476/702) 

**Internal ID** \#1-3-260

Created at 2020-08-13 11:39:53

Tags: `python` `python-3.x` `beautifulsoup`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I was trying to run a simple python file using BeautifulSoup when I received the following error message in Atom.

I have already installed BeautifulSoup using the following command in my Macbook's terminal.
```
$ pip3 install beautifulsoup4
```

I want to note that I have both Python 2.7.10 and Python 3.8.5 installed. I looked at my installed modules, and I only see 'bs4' in the modules for python3 and not in python.
When I try to place  in Atom or PyCharm, both text editors say that the module  exist. Any help would be much appreciated, thank you.



```
import requests
from bs4 import BeautifulSoup
```



```
Traceback (most recent call last):
  File "/Users/lyons/Documents/scrape/scrape.py", line 2, in <module>
    from bs4 import BeautifulSoup
ModuleNotFoundError: No module named 'bs4'
[Finished in 2.237s]
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-08-13 17:47:42

------------

None of the previous answers managed to fix my error, but an ugly solution that I found was to just add the following code to the start of the file.
```
import subprocess
import sys

def install(package):
    subprocess.check_call([sys.executable, "-m", "pip", "install", bs4])

from bs4 import BeautifulSoup
```



------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-08-13 11:43:08

------------

Try to install in particular version of Python
```
python2.7 -m pip install beautifulsoup4

python3.8 -m pip install beautifulsoup4
```



------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-02-02 22:52:23

------------

If anyone runs into this - bs4 behaves unexpectedly when working with subprocess (my setup includes venv). What worked for me was a revision of the suggested solution:
```
def install_bs4():
    subprocess.check_call([sys.executable, "-m", "pip", "install", "bs4"])

try:
    from bs4 import BeautifulSoup
except:
    install_bs4()
    from bs4 import BeautifulSoup
```



------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-08-13 11:47:57

------------

If you are on a Unix-based system, try running from root:
```
sudo pip3 install beautifulsoup4
```



------------
    
    