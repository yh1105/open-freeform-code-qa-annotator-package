
# Post \#54392995 [Link](https://stackoverflow.com/questions/54392995/)

## RemoveError: 'requests' is a dependency of conda and cannot be removed from conda's operating environment

**Vote**: 64 (104/702) **Views**: 60460 (141/702) 

**Internal ID** \#1-3-202

Created at 2019-01-27 21:22:57

Tags: `python` `windows` `conda`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Having trouble installing a third party library and I have not seen this error before using Windows 10 with Anaconda installed:

```
C:\Users\XYZ>conda env create -f python3.6-environment-windows.yml
Collecting package metadata: done
Solving environment: done

Downloading and Extracting Packages
certifi-2018.1.18    | 144 KB    | ############################################################################ | 100%
mkl-2018.0.1         | 155.2 MB  | ############################################################################ | 100%
pytz-2018.9          | 229 KB    | ############################################################################ | 100%
icc_rt-2019.0.0      | 9.4 MB    | ############################################################################ | 100%
icu-58.2             | 21.8 MB   | ############################################################################ | 100%
pip-9.0.1            | 1.7 MB    | ############################################################################ | 100%
xz-5.2.3             | 348 KB    | ############################################################################ | 100%
sip-4.18.1           | 269 KB    | ############################################################################ | 100%
libpng-1.6.36        | 1.3 MB    | ############################################################################ | 100%
vc-14                | 985 B     | ############################################################################ | 100%
numpy-1.14.0         | 3.7 MB    | ############################################################################ | 100%
python-3.6.4         | 17.6 MB   | ############################################################################ | 100%
jpeg-9c              | 314 KB    | ############################################################################ | 100%
wheel-0.30.0         | 85 KB     | ############################################################################ | 100%
wincertstore-0.2     | 13 KB     | ############################################################################ | 100%
freetype-2.9.1       | 475 KB    | ############################################################################ | 100%
scipy-1.0.0          | 13.0 MB   | ############################################################################ | 100%
pyparsing-2.3.1      | 54 KB     | ############################################################################ | 100%
kiwisolver-1.0.1     | 60 KB     | ############################################################################ | 100%
qt-5.6.2             | 55.6 MB   | ############################################################################ | 100%
python-dateutil-2.7. | 218 KB    | ############################################################################ | 100%
vs2015_runtime-14.0. | 1.9 MB    | ############################################################################ | 100%
ca-certificates-2017 | 489 KB    | ############################################################################ | 100%
tk-8.6.7             | 3.5 MB    | ############################################################################ | 100%
setuptools-38.4.0    | 540 KB    | ############################################################################ | 100%
matplotlib-2.2.2     | 6.5 MB    | ############################################################################ | 100%
six-1.12.0           | 21 KB     | ############################################################################ | 100%
openssl-1.0.2n       | 5.4 MB    | ############################################################################ | 100%
pyqt-5.6.0           | 4.5 MB    | ############################################################################ | 100%
zlib-1.2.11          | 236 KB    | ############################################################################ | 100%
tornado-5.1.1        | 665 KB    | ############################################################################ | 100%
sqlite-3.22.0        | 907 KB    | ############################################################################ | 100%
cycler-0.10.0        | 8 KB      | ############################################################################ | 100%
Preparing transaction: done
Verifying transaction: failed

RemoveError: 'requests' is a dependency of conda and cannot be removed from
conda's operating environment.
RemoveError: 'setuptools' is a dependency of conda and cannot be removed from
conda's operating environment.
```


In reference to the instructions here - [https://enigma.co/catalyst/install.html#installing-with-conda](https://enigma.co/catalyst/install.html#installing-with-conda)


----------
        
## Answer \#0

**Accepted** Vote: 38

Created at 2019-02-13 21:35:39

------------

running `conda update conda` before solved the problem for me


------------
    
    
## Answer \#1

 Vote: 71

Created at 2019-02-21 06:10:21

------------

I had the same problem on Mac Mojave, and in my case run `conda update --force conda` first worked for me.


------------
    
    
## Answer \#2

 Vote: 14

Created at 2019-10-03 17:52:12

------------

```
conda update --force conda
```


will solve : Verifying transaction: failed Remove Error: 'request' is a dependency of conda and cannot be removed from conda is operating environment.


------------
    
    
## Answer \#3

 Vote: 12

Created at 2019-06-18 13:55:44

------------

This worked for me on Linux:


1. Uninstall the package via pip: pip uninstall setuptools
2. Reinstall using the following command: conda install -c anaconda setuptools



Consider also that system packages (`python3-setuptools`, `python-setuptools` in Ubuntu) might need to be removed.


------------
    
    
## Answer \#4

 Vote: 8

Created at 2020-04-28 03:10:32

------------

```
conda update --force conda
```


this worked for me in win64 when I  

```
conda install -c https://conda.anaconda.org/sdvillal openslide-python
```



------------
    
    
## Answer \#5

 Vote: 3

Created at 2019-11-30 22:05:49

------------

Silly point to make here but I've been caught by this before: . Just before killing off any package run a quick check to see:

```
conda env list
```


Performing ops on the base environment can cause issues, so you may [want to create or change environment](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).


------------
    
    
## Answer \#6

 Vote: 1

Created at 2019-01-28 13:37:16

------------

I had the same problem, which was due to a bad installation of a package (OpenEXR in my case).
Try to verify your package with `conda list yourPackage`.
If you find which one it is, prefer installing it with pre-built packages here : [https://www.lfd.uci.edu/~gohlke/pythonlibs/](https://www.lfd.uci.edu/~gohlke/pythonlibs/).

A stack-overflow thread that helped me : [Python 2.7, PIP: "Failed building wheel for ..."](https://stackoverflow.com/questions/38320033/python-2-7-pip-failed-building-wheel-for)

EDIT : 
Two friends had the same error, one solved it by installing/executing with admin rights.
The other one succeeded by creating a new virtual environnement for python.


------------
    
    
## Answer \#7

 Vote: 1

Created at 2019-01-30 12:10:02

------------

I ran into the same problem, on macos, after removing all R packages installed by anaconda. Unfortunately, the only solution I found is to reinstall anaconda. Alternatively, you could download miniconda shell script installer, run it with `-U` option and point the installer at your conda directory. This fixed the issue for me while maintaining settings such as channel priority. However, many of previously installed packages were not importable (Python couldn't find them) and i ended up re-installing all of them.


------------
    
    
## Answer \#8

 Vote: 1

Created at 2019-02-05 20:13:39

------------

I had the same problem. Try to use the command prompt to install wheel file if you get an error something like this(as shown in picture) then you must install that wheel file:

[](https://i.stack.imgur.com/C1iem.png)

Extra(how to install wheel file from command prompt):

> - [https://www.lfd.uci.edu/~gohlke/pythonlibs/](https://www.lfd.uci.edu/~gohlke/pythonlibs/)- - - - - 


------------
    
    
## Answer \#9

 Vote: 1

Created at 2023-02-14 11:45:59

------------

I had to do the following steps to get it to work for me
```
conda update --force conda
```

This will run and update conda then run the following command
```
conda update conda
```

At each stage update all packages. After that, everything should work fine.


------------
    
    
## Answer \#10

 Vote: 0

Created at 2020-06-09 10:12:01

------------

try conda env create --force -f python3.6-environment-windows.yml
I'm not sure exactly where to place it but I use the --force whenever I run into issues like this like conda update --force conda or conda install --force ...
use it as last solution since it force conda to perform the command and you might run into some issues later on 


------------
    
    
## Answer \#11

 Vote: 0

Created at 2020-07-02 14:25:32

------------

I had solved the problem
RemoveError: 'setuptools' is a dependency of conda and cannot be removed from
conda's operating environment.
![enter image description here](https://i.stack.imgur.com/aHKej.png)
As the following way:
- - - 
![enter image description here](https://i.stack.imgur.com/0kRA2.png)
and then check tools
![enter image description here](https://i.stack.imgur.com/QmwGc.png)
I haven't met any mistakes yet, but help me correct my way , if something wrong, please


------------
    
    
## Answer \#12

 Vote: 0

Created at 2022-02-21 06:40:45

------------

For me, deactivating the current conda environment solves the problem, i.e. run `conda deactivate` before upgrading.


------------
    
    