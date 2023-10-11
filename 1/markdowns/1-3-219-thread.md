
# Post \#63707774 [Link](https://stackoverflow.com/questions/63707774/)

## Unable to start Jupyter notebook on VS Code

**Vote**: 3 (575/702) **Views**: 22499 (277/702) 

**Internal ID** \#1-3-219

Created at 2020-09-02 14:31:36

Tags: `python` `visual-studio-code` `jupyter-notebook` `miniconda`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

(Using latest miniconda + VS Code on 64-bit Windows10:) After clean reinstalling VS Code, I can no longer launch jupyter notebook from within it. When I tried to create a new jupyter file for the first time, the Python extension installed `ipykernel` in my virtual environment "da38" (my main working environment). Then it stays on `Connecting to IPython kernel: Connecting to kernel` for an unusually long time, and stops with the error message `Unable to start session for kernel Python 3.8.5 64-bit ('da38':conda)` (images pasted below). I also removed and re-created da38 environment just in case. Used jupyter many times before with no issues until this new VS Code install today, and an identical setup is working on my other computer. Any help is appreciated. Thanks!
[](https://i.stack.imgur.com/NM3I8.png)
[](https://i.stack.imgur.com/jNOG7.png)


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2020-09-03 07:52:52

------------

This issue also occurs on my computer. The solution is to restore the version number of a dependency package "`traitlets`" of ipython kernel to `4.3.3`.
You could try to use "`conda list`" to view the version of the module traitlets in the current conda environment. If it shows version 5.0, it is recommended that you use version 4.3.3.
[](https://i.stack.imgur.com/mkRmX.png)
Reference: [Unable to start session for kernel Python](https://github.com/microsoft/vscode-python/issues/13701#issuecomment-684994934).


------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-09-03 20:20:10

------------

I had the latest version and below command worked for me
```
python -m pip install 'traitlets==4.3.3' --force-reinstall
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-02-10 18:44:18

------------

I had this same problem. What worked for me is updating ipykernel.
```
$ pip install ipykernel --upgrade
```

I also did a few other upgrades just in case.
```
$ pip install traitlets --upgrade
$ pip install notebook --upgrade
```

I did not role back to an older version like 'traitlets==4.3.3'.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2022-05-06 06:56:44

------------

If anyone facing same issues now, Can check below steps which helped me to solve this
- - 


------------
    
    
## Answer \#4

 Vote: 0

Created at 2023-02-12 02:49:39

------------

We need to update "ipykernel" and restart VSC:
```
pip install ipykernel --upgrade
```



------------
    
    