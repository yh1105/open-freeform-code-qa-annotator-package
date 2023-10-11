
# Post \#62325068 [Link](https://stackoverflow.com/questions/62325068/)

## Cannot install latest nodejs using conda on Mac

**Vote**: 23 (239/702) **Views**: 30006 (232/702) 

**Internal ID** \#1-3-305

Created at 2020-06-11 13:02:27

Tags: `node.js` `conda` `conda-forge`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `javascript` `python`)

----------

**Notepad**


----------

When I run `conda search -c conda-forge nodejs`, I get:

```
# Name                       Version           Build  Channel
...
nodejs                        14.2.0      h2c41780_1  conda-forge
nodejs                        14.2.0      h38d8c5a_0  conda-forge
nodejs                        14.3.0      h2c41780_0  conda-forge
nodejs                        14.4.0      h2c41780_0  conda-forge
```


But when I try to install it using `conda install -c conda-forge nodejs` it tries to install 6.13.1. Doing `conda install -c conda-forge nodejs==14.4.0` also does not work. It keeps trying to "solve" the environment. Any suggestions on how to fix this?


----------
        
## Answer \#0

**Accepted** Vote: 46

Created at 2020-06-13 07:58:21

------------

Currently, the latest version nodejs 14.x requires `icu>=65` which is not yet globally available across conda-forge packages. Therefore it can be installed into a new environment with `conda create -n new_env_name -c conda-forge nodejs`, but most likely will raise package conflicts in existing environments.

`conda install node-js -c conda-forge` installing the very old version `6.13.1` seems to be a solver problem. `conda install nodejs -c conda-forge --repodata-fn=repodata.json` will install a more current version: nodejs-13.x.

Alternative workaround is to use [mamba](https://github.com/QuantStack/mamba) as a conda replacement. 

Credits go to Wolf Vollprecht.


------------
    
    
## Answer \#1

 Vote: 12

Created at 2021-05-03 15:37:06

------------

I had a similar problem and this is how I fixed it:
First I did not only `conda install -c conda-forge nodejs`,  that are listed in the [https://anaconda.org/conda-forge/nodejs](https://anaconda.org/conda-forge/nodejs).
```
conda install -c conda-forge nodejs
conda install -c conda-forge/label/gcc7 nodejs
conda install -c conda-forge/label/cf201901 nodejs
conda install -c conda-forge/label/cf202003 nodejs
```

With this I got 13.10.1 version of . And after I did `conda update nodejs` and received 14.8.0 version of it.


------------
    
    
## Answer \#2

 Vote: 8

Created at 2020-09-01 00:49:15

------------

Here's my workaround:
I installed nodejs for Mac from the .pkg file from [the offical site](https://nodejs.org/en/download/current/) and then every time I update packages in my conda environments, I force remove the nodejs version conda installs with:
```
conda uninstall --force nodejs
```

and then go about using the environment as if it had nodejs installed. That way when any other package needs nodejs, it gets the latest one from the system directory /usr/local/.


------------
    
    
## Answer \#3

 Vote: 3

Created at 2020-12-16 14:28:15

------------

NodeJS 15.3 successfully installed as of today, with:
```
conda install -c conda-forge nodejs
```

On a miniconda 4.9.2, python 3.9 environment.
As detail, the `icu==68.1` get installed together.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2022-02-16 15:46:41

------------

Just try this:
```
conda upgrade -c conda-forge nodejs
```



------------
    
    