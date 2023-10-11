
# Post \#58766671 [Link](https://stackoverflow.com/questions/58766671/)

## Cannot install librosa python, how can I uninstall llvmlite?

**Vote**: 10 (387/702) **Views**: 20702 (298/702) 

**Internal ID** \#1-3-308

Created at 2019-11-08 12:32:26

Tags: `python` `librosa`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have recently tried to install `librosa` on Windows using `pip install librosa`. Nevertheless, this error appears:

> Cannot uninstall 'llvmlite'. It is a distutils installed project and
  thus we cannot accurately determine which files belong to it which
  would lead to only a partial uninstall.

As a solution, I have executed `conda update --all`, and `conda install numba=0.39.0`, without any improvement.

Thank you in advanced.


----------
        
## Answer \#0

**Accepted** Vote: 25

Created at 2019-11-08 21:06:45

------------

You just have to type the command you want to execute with the `--user` tag. 
In your case, it would be:

```
pip install librosa --user
```



------------
    
    
## Answer \#1

 Vote: 7

Created at 2019-11-08 13:48:05

------------

Let me try to help.

I recently faced that problem and get rid from it by using following command.

```
--ignore-installed
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2021-09-24 07:49:45

------------

You can use:
```
pip install librosa --ignore-installed llvmlite
```



------------
    
    