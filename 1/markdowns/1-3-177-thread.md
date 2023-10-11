
# Post \#65007143 [Link](https://stackoverflow.com/questions/65007143/)

## pyqt5_tools designer.exe does not exist

**Vote**: 2 (631/702) **Views**: 5579 (508/702) 

**Internal ID** \#1-3-177

Created at 2020-11-25 14:55:23

Tags: `python` `pip`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have installed PyQT5 by command `pip install pyqt5 pyqt5-tools`. Then I want to show path for designer.exe. However I could not found that in  directory. These are content of that folder.
[](https://i.stack.imgur.com/q6Vl2.png)


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-11-25 15:00:04

------------

On my system QT Designer is saved under `C:\Users\User\AppData\Local\Qt Designer`
EDIT:
It seems like I installed QT Designer differently.
You can use `pip install PyQt5Designer`.
Then it should be in the path I gave.


------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-07-06 12:23:48

------------

using the `pip install pyqt5-tools` method I found the designer on this path:
C:\Users\user\AppData\Local\Programs\Python\Python39\Lib\site-packages\qt5_applications\Qt\bin


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-11-22 01:51:34

------------

`pip install pyqt5-tools`
Check the path `your_python_installed\Lib\site-packages\qt5_applications\Qt\bin\designer.exe`


------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-11-23 18:09:18

------------

```
pip install pyqt5-tools
```

I Found designer.exe in:
```
%APPDATA%\Roaming\Python\[Version]\site-packages\qt5_applications\Qt\bin
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-02-11 10:38:20

------------

I found it on path
venv\Lib\site-packages\qt5_applications\Qt\bin


------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-06-01 15:01:00

------------

If this helps someone, in my case there were two "similar" of pyqt5 folders inside site-packages, ones starts with pyqt5 and others with just qt5, I found the designer app in the folder qt5_aplications.


------------
    
    