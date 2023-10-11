
# Post \#52816156 [Link](https://stackoverflow.com/questions/52816156/)

## How to create virtual environment for python 3.7.0?

**Vote**: 24 (233/702) **Views**: 103027 (81/702) 

**Internal ID** \#1-3-282

Created at 2018-10-15 11:51:18

Tags: `linux` `virtualenv` `python-3.7`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm able to install it with root user but I wanted to install it in a clean environment. My use case is to test the installation of another application with pip for the customer who is using python3.7.0
> sudo apt-get updatesudo apt-get install build-essential libpq-dev libssl-dev openssl libffi-dev zlib1g-devsudo apt-get install python3-pip python3-devsudo apt-get install python3.7
Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 54

Created at 2018-10-15 14:05:02

------------

(assuming `python3.7` is installed)

Install `virtualenv` package:

```
pip3.7 install virtualenv
```


Create new environment:

```
python3.7 -m virtualenv MyEnv
```


Activate environment:

```
source MyEnv/bin/activate
```



------------
    
    
## Answer \#1

 Vote: 37

Created at 2019-11-01 18:36:36

------------

To help anyone else who runs into the chicken & egg situation trying to use the above chosen answer, here's what solved it for me:

```
sudo apt install python3.7-venv
python3.7 -m venv env37
source env37/bin/activate
deactivate (when done using the environment)
```


I had installed python 3.7 using deadsnakes vs source:

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python3.7
```


In doing so I could run python3.7 --version but since I had no pip3.7 I could not install virtualenv as directed in the solution above. Luck would have it that deadsnakes has venv! Once I installed venv I could create my environment & be on my merry way

[Handy official python page with venv info](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)

So why didn't I use?:
    python3.7 -m ensurepip

That was giving me:

> ERROR: Could not install packages due to an EnvironmentError: [Errno 13] Permission denied: '/usr/local/lib/python3.7/dist-packages/easy_install.py'
  Consider using the `--user` option or check the permissions.

Which left me with 3 choices: 

> use sudo (which is simple but I keep being told is frowned upon)
  install with --user option which wasn't ideal in that I may not always be logged in as the same user
  or install it in an environment which I'm told is the recommended route.

But see chicken egg above.. How do I install pip in environment when I can't create venv or virtualenv?  Thus my workaround solution of installing venv from deadsnakes which allowed me to create the virtual environment to then install pip3.7:

```
(env37) user@ubuntu:~$ python3.7 -m ensurepip
(env37) user@ubuntu:~$ pip3.7 --version
pip 19.2.3 from /home/user/env37/lib/python3.7/site-packages/pip (python 3.7)
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-05-12 02:40:28

------------

Some added information, if you are trying for some version like python 3.7.10, which might give following error upon executing `pip3.7.10 install virtualenv`
`.pyenv/versions/3.7.10/bin/python: No module named virtualenv`
So, in a general sense you can do the following steps:
[commands are specific to MacOs, I am currently using with the new M1 chip]

1. After installing 3.7.10 using pyenv, make it global.


```
brew update
brew install pyenv
```

set environment variables
```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
source ~/.bash_profile
```

look at the pyenv list to see if the version you install is there or not and install and make it global
```
pyenv install --list
pyenv install 3.7.10
pyenv global 3.7.10
```


1. create your virtual environment now with this version


```
python -m venv MyEnv
```


1. activate it


```
source MyEnv/bin/activate
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2020-03-22 06:25:22

------------

Using pip on windows, you can do the following:

1.`virtualenv --python "C:\\Python37\\python.exe" venv`# use your own path

You will see something like this:

Running virtualenv with interpreter C:\Python37\python.exe
Using base prefix 'C:\Python37'
New python executable in C:\Users\XXXX\Documents\GitHub\MyProject\venv\Scripts\python.exe
Installing setuptools, pip, wheel...
done.

2.`C:\Users\XXXXX\Documents\GitHub\MyProject>cd venv C:\Users\XXXXX\Documents\GitHub\MyProject\venv>cd Scripts C:\Users\XXXXX\Documents\GitHub\MyProject\venv\Scripts>activate`.

At the beginning of the command path, when you see (environment variable name) in this case (venv), this is a sign that your virtual environment is activated. 
`(venv) C:\Users\tuscar2001\Documents\GitHub\MyProject\venv\Scripts>`

Please check the following link for more details:[http://www.datasciencetopics.com/2020/03/how-to-set-up-virtual-environment-in.html](http://www.datasciencetopics.com/2020/03/how-to-set-up-virtual-environment-in.html)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-11-26 21:04:11

------------

Figure out python3.7 path on your system. For mac with python3.7 in brew you can use the following
```
virtualenv env -p /usr/local/opt/python@3.7/bin/python3
source ./env/bin/activate
```



------------
    
    