
# Post \#51125013 [Link](https://stackoverflow.com/questions/51125013/)

## How can I install a previous version of Python 3 in macOS using homebrew?

**Vote**: 157 (31/702) **Views**: 182005 (42/702) 

**Internal ID** \#1-3-239

Created at 2018-07-01 16:20:28

Tags: `python` `macos` `homebrew`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

How can I install a previous version of Python 3 in macOS using brew?

With the command `brew install python` I got the latest version of Python 3 (currently v3.7.0), but I want the last version of Python 3.6 (currently 3.6.5).

I have read about another package `pyenv` that can assist in handle different python installation, but this solution is not suitable for me.


----------
        
## Answer \#0

**Accepted** Vote: 268

Created at 2018-07-01 16:20:28

------------


## Short Answer



To make a clean install of Python 3.6.5 use:

```
brew unlink python # ONLY if you have installed (with brew) another version of python 3
brew install --ignore-dependencies https://raw.githubusercontent.com/Homebrew/homebrew-core/f2a764ef944b1080be64bd88dca9a1d80130c558/Formula/python.rb
```


If you prefer to recover a previously installed version, then:

```
brew info python           # To see what you have previously installed
brew switch python 3.x.x_x # Ex. 3.6.5_1
```



## Long Answer



There are two formulas for installing Python with Homebrew: `python@2` and `python`.
The first is for Python 2 and the second for Python 3.

 You can find outdated answers on the web where it is mentioned `python3` as the formula name for installing Python version 3. Now it's just `python`!

By default, with these formulas you can install the latest version of the corresponding major version of Python. So, you cannot directly install a minor version like 3.6.


### Solution



With `brew`, you can install a package using the address of the formula, for example in a git repository.

```
brew install https://the/address/to/the/formula/FORMULA_NAME.rb
```


Or specifically for Python 3

```
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/COMMIT_IDENTIFIER/Formula/python.rb
```


The address you must specify is the address to the last commit of the formula (python.rb) for the desired version.
You can find the commint identifier by looking at the history for homebrew-core/Formula/python.rb

[https://github.com/Homebrew/homebrew-core/commits/master/Formula/python.rb](https://github.com/Homebrew/homebrew-core/commits/master/Formula/python.rb)


## Python > 3.6.5



In the link above you will not find a formula for a version of Python above 3.6.5.
After the maintainers of that (official) repository released Python 3.7, they only submit updates to the recipe of Python 3.7.

As explained above, with homebrew you have only Python 2 (python@2) and Python 3 (python), .

Although those minor updates are mostly irrelevant in most cases and for most users, I will search if someone has done an explicit formula for 3.6.


------------
    
    
## Answer \#1

 Vote: 39

Created at 2020-02-12 13:55:58

------------

I have tried everything but could not make it work. Finally I have used `pyenv` and it worked directly like a charm.
So having `homebrew` installed, juste do:
```
brew install pyenv
pyenv install 3.6.5
```

to manage virtualenvs:
```
brew install pyenv-virtualenv
pyenv virtualenv 3.6.5 env_name
```

See [pyenv](https://github.com/pyenv/pyenv) and [pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv) for more info.

### EDIT (2020/03/19)


I have found using the [pyenv-installer](https://github.com/pyenv/pyenv-installer) easier than homebrew to install pyenv and pyenv-virtualenv direclty:
```
curl https://pyenv.run | bash
```

To manage python version, either globally:
```
pyenv global 3.6.5
```

or locally in a given directory:
```
pyenv local 3.6.5
```



------------
    
    
## Answer \#2

 Vote: 32

Created at 2019-01-22 10:22:24

------------

As an update, when doing 

```
brew unlink python # If you have installed (with brew) another version of python
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/f2a764ef944b1080be64bd88dca9a1d80130c558/Formula/python.rb
```


You may encounter

```
Error: python contains a recursive dependency on itself:
  python depends on sphinx-doc
  sphinx-doc depends on python
```


To bypass it, add the `--ignore-dependencies` argument to brew install.

```
brew unlink python # If you have installed (with brew) another version of python
brew install --ignore-dependencies https://raw.githubusercontent.com/Homebrew/homebrew-core/f2a764ef944b1080be64bd88dca9a1d80130c558/Formula/python.rb
```



------------
    
    
## Answer \#3

 Vote: 29

Created at 2019-01-30 15:22:53

------------

What I did was first I installed python 3.7 and then unlinked it using the following commands
```
brew install python3
brew unlink python
```

then I installed python 3.6.5 using the following command taken from above answer.
```
brew install --ignore-dependencies https://raw.githubusercontent.com/Homebrew/homebrew-core/f2a764ef944b1080be64bd88dca9a1d80130c558/Formula/python.rb --ignore-dependencies
```

After that I ran the following command
```
brew link --overwrite python
```

Now I have all pythons in the system. To find out run
```
mian@tdowrick2~ $ python --version
Python 2.7.10
mian@tdowrick2~ $ python3.7 --version
Python 3.7.1
mian@tdowrick2~ $ python3.6 --version
Python 3.6.5
```

To create Python 3.7 virtual environment.
```
mian@tdowrick2~ $ virtualenv -p python3.7 env
Already using interpreter /Library/Frameworks/Python.framework/Versions/3.7/bin/python3.7
Using base prefix '/Library/Frameworks/Python.framework/Versions/3.7'
New python executable in /Users/mian/env/bin/python3.7
Also creating executable in /Users/mian/env/bin/python
Installing setuptools, pip, wheel...
done.
mian@tdowrick2~ $ source env/bin/activate
(env) mian@tdowrick2~ $ python --version
Python 3.7.1
(env) mian@tdowrick2~ $ deactivate
```

To create Python 3.6 virtual environment
```
mian@tdowrick2~ $ virtualenv -p python3.6 env
Running virtualenv with interpreter /usr/local/bin/python3.6
Using base prefix '/usr/local/Cellar/python/3.6.5_1/Frameworks/Python.framework/Versions/3.6'
New python executable in /Users/mian/env/bin/python3.6
Not overwriting existing python script /Users/mian/env/bin/python (you must use /Users/mian/env/bin/python3.6)
Installing setuptools, pip, wheel...
done.
mian@tdowrick2~ $ source env/bin/activate
(env) mian@tdowrick2~ $ python --version
Python 3.6.5
(env) mian@tdowrick2~ $ deactivate
```



------------
    
    
## Answer \#4

 Vote: 15

Created at 2019-05-14 16:39:39

------------

I tried all the answers above to install Python 3.4.4. The installation of python worked, but PIP would not be installed and nothing I could do to make it work. I was using Mac OSX Mojave, which cause some issues with zlib, openssl.



- - 




1. Download the macOS 64-bit installer or macOS 64-bit/32-bit installer: https://www.python.org/downloads/release/python-365/
2. In previous step, it will download Python 3.6.5, if for example, you want to download Python 3.4.4, replace in the url above python-365 by python-344
3. Download click on the file you downloaded a GUI installer will open
4. If you downloaded python-365, after installation, to launch this version of python, you will type in your terminal python365, same thing for pip, it will be pip365



p.s: You don't have to uninstall your other version of Python on your system.


---






---



I found a much much much better solution that work on MacOSX, Windows, Linux, etc.


1. It doesn't matter if you have already python installed or not.
2. Download Anaconda
3. Once installed, in terminal type: conda init
4. In terminal,create virtual environment with any python version, for example, I picked 3.4.4: conda create -n [NameOfYour VirtualEnvironment] python=3.4.4
5. Then, in terminal, you can check all the virtual environment you ahave created with the command: conda info --envs
6. Then, in terminal, activate the virtual environment of your choice with: conda activate [The name of your virtual environment that was shown with the command at step 5]




------------
    
    
## Answer \#5

 Vote: 8

Created at 2020-03-20 03:52:20

------------

In case anyone face pip issue like below

```
pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.
```


The root cause is openssl 1.1 doesn’t support python 3.6 anymore. So you need to install old version openssl 1.0

here is the solution:

```
brew uninstall --ignore-dependencies openssl
brew install https://github.com/tebelorg/Tump/releases/download/v1.0.0/openssl.rb
```



------------
    
    
## Answer \#6

 Vote: 1

Created at 2018-10-10 00:09:35

------------

To solve this with `homebrew`, you can temporarily backdate `homebrew-core` and set the `HOMEBREW_NO_AUTO_UPDATE` variable to hold it in place:

```
cd `brew --repo homebrew/core`
git checkout f2a764ef944b1080be64bd88dca9a1d80130c558
export HOMEBREW_NO_AUTO_UPDATE=1
brew install python
```


I don't recommend permanently backdating homebrew-core, as you will miss out on security patches, but it is useful for testing purposes.

You can also extract old versions of homebrew formulae into your own tap (tap_owner/tap_name) using the `brew extract` command:

```
brew extract python tap_owner/tap_name --version=3.6.5
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2020-07-24 17:00:41

------------

The easiest way for me was to install Anaconda: [https://docs.anaconda.com/anaconda/install/](https://docs.anaconda.com/anaconda/install/)
There I can create as many environments with different Python versions as I want and switch between them with a mouse click. It could not be easier.
To install different Python versions just follow these instructions [https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-python.html)
A new development environment with a different Python version was done within 2 minutes. And in the future I can easily switch back and forth.


------------
    
    