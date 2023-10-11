
# Post \#58648739 [Link](https://stackoverflow.com/questions/58648739/)

## How to check if python package is latest version programmatically?

**Vote**: 49 (123/702) **Views**: 15585 (350/702) 

**Internal ID** \#1-3-223

Created at 2019-10-31 17:43:54

Tags: `python` `pip` `gekko`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

How do you check if a package is at its latest version programmatically in a script and return a true or false?

I can check with a script like this:

```
package='gekko'
import pip
if hasattr(pip, 'main'):
    from pip import main as pipmain
else:
    from pip._internal import main as pipmain
pipmain(['search','gekko'])
```


or with command line: 

```
(base) C:\User>pip search gekko
gekko (0.2.3)  - Machine learning and optimization for dynamic systems
  INSTALLED: 0.2.3 (latest)
```


But how do I check programmatically and return true or false?


----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2019-10-31 18:21:34

------------


# Fast Version (Checking the package only)


The code below calls the package with an unavailable version like `pip install package_name==random`. The call returns all the available versions. The program reads the latest version.
The program then runs `pip show package_name` and gets the current version of the package.
If it finds a match, it returns True, otherwise False.
This is a reliable option given that it stands on `pip`
```
import subprocess
import sys
def check(name):
    latest_version = str(subprocess.run([sys.executable, '-m', 'pip', 'install', '{}==random'.format(name)], capture_output=True, text=True))
    latest_version = latest_version[latest_version.find('(from versions:')+15:]
    latest_version = latest_version[:latest_version.find(')')]
    latest_version = latest_version.replace(' ','').split(',')[-1]

    current_version = str(subprocess.run([sys.executable, '-m', 'pip', 'show', '{}'.format(name)], capture_output=True, text=True))
    current_version = current_version[current_version.find('Version:')+8:]
    current_version = current_version[:current_version.find('\\n')].replace(' ','') 

    if latest_version == current_version:
        return True
    else:
        return False
```


### Edit 2021: The code below no longer works with the new version of pip


The following code calls for `pip list --outdated`:
```
import subprocess
import sys

def check(name):
    reqs = subprocess.check_output([sys.executable, '-m', 'pip', 'list','--outdated'])
    outdated_packages = [r.decode().split('==')[0] for r in reqs.split()]
    return name in outdated_packages
```



------------
    
    
## Answer \#1

 Vote: 15

Created at 2019-10-31 18:39:36

------------

My project [johnnydep](https://github.com/wimglenn/johnnydep) has this feature.

In shell:

```
pip install --upgrade pip johnnydep
pip install gekko==0.2.0
```


In Python:

```
>>> from johnnydep.lib import JohnnyDist
>>> dist = JohnnyDist("gekko")
>>> dist.version_installed  
'0.2.0'
>>> dist.version_latest 
'0.2.3'
```



------------
    
    
## Answer \#2

 Vote: 11

Created at 2020-06-25 08:42:39

------------

To just check the latest version on PyPi (for Python 3.6 and up):
```
pip install requests
```

Then:
```
import requests

package = 'django'  # replace with the package you want to check
response = requests.get(f'https://pypi.org/pypi/{package}/json')
latest_version = response.json()['info']['version']
```



------------
    
    
## Answer \#3

 Vote: 10

Created at 2019-11-01 14:55:14

------------


## Checking Installed version:


One way to check installed version is just to access the `__version__` attribute of the top-level namespace:
```
>>> import gekko
>>> gekko.__version__
'0.2.0'
```

Unfortunately not all projects set this attribute, it's just a common convention in Python. When they don't have a version attribute, you can use [importlib.metadata](https://docs.python.org/3/library/importlib.metadata.html#distribution-versions) to query the package version. This way does not actually require importing the package itself, since it's retrieved from the package metadata which gets written out when the package was installed.
```
>>> import importlib.metadata
>>> importlib.metadata.version("gekko")
'0.2.0'
```

This functionality is available since Python 3.8. In older Python versions, you can use `pkg_resources` similarly, which is a part of `setuptools`:
```
>>> import pkg_resources
>>> pkg_resources.get_distribution("gekko").version
'0.2.0'
```


## Checking Latest version:


There isn't currently a way to do this within stdlib. But my project [luddite](https://github.com/jumptrading/luddite) has this feature:
```
>>> import luddite
>>> luddite.get_version_pypi("gekko")
'0.2.3'
```



------------
    
    
## Answer \#4

 Vote: 2

Created at 2019-10-31 18:13:07

------------

This should do the trick at least for demo purposes. Simply call `isLatestVersion` with the name of the package you would like to check. If you are using this somewhere important you would want to try/catch the url request as internet access may not be available. Also note that if the package is not installed `isLatestVersion` will return False.

This is tested for Python 3.7.4 and Pip 19.0.3.

```
import pip
import subprocess
import json
import urllib.request
from pip._internal.operations.freeze import freeze

def isLatestVersion(pkgName):
    # Get the currently installed version
    current_version = ''
    for requirement in freeze(local_only=False):
        pkg = requirement.split('==')
        if pkg[0] == pkgName:
            current_version = pkg[1]

    # Check pypi for the latest version number
    contents = urllib.request.urlopen('https://pypi.org/pypi/'+pkgName+'/json').read()
    data = json.loads(contents)
    latest_version = data['info']['version']

    return latest_version == current_version
```



------------
    
    
## Answer \#5

 Vote: 2

Created at 2019-10-31 19:12:03

------------



Thanks for the several suggestions. Here is a new version that doesn't use `pip search` but instead pulls the latest version directly from `pypi` as proposed by [Daniel Hill](https://stackoverflow.com/users/8200392/daniel-hill). This also resolves the issue with the substring false matches.

```
def check(name):
    import subprocess
    import sys
    import json
    import urllib.request

    # create dictionary of package versions
    pkgs = subprocess.check_output([sys.executable, '-m', 'pip', 'freeze'])
    keys = [p.decode().split('==')[0] for p in pkgs.split()]
    values = [p.decode().split('==')[1] for p in pkgs.split()]
    d = dict(zip(keys, values)) # dictionary of all package versions

    # retrieve info on latest version
    contents = urllib.request.urlopen('https://pypi.org/pypi/'+name+'/json').read()
    data = json.loads(contents)
    latest_version = data['info']['version']

    if d[name]==latest_version:
        print('Latest version (' + d[name] + ') of '+str(name)+' is installed')
        return True
    else:
        print('Version ' + d[name] + ' of '+str(name)+' not the latest '+latest_version)
        return False

print(check('gekko'))
```




Here is a fast solution that retrieves latest version information on only the `gekko` package of interest.

```
def check(name):
    import subprocess
    import sys
    # create dictionary of package versions
    pkgs = subprocess.check_output([sys.executable, '-m', 'pip', 'freeze'])
    keys = [p.decode().split('==')[0] for p in pkgs.split()]
    values = [p.decode().split('==')[1] for p in pkgs.split()]
    d = dict(zip(keys, values)) # dictionary of all package versions

    # retrieve info on latest version
    s = subprocess.check_output([sys.executable, '-m', 'pip', 'search', name])

    if d[name] in s.decode():  # weakness
        print('Latest version (' + d[name] + ') of '+str(name)+' is installed')
        return True
    else:
        print(s.decode())
        return False

print(check('gekko'))
```


This produces the message `Latest version (0.2.3) of gekko is installed` and returns `True` to indicate latest version (or `False` if not the latest version). This may not be the best solution because it only checks for a version substring with `if d[name] in s.decode():` but it is faster than `pip list --outdated` that checks all the packages. This isn't the most reliable method because it will return an incorrect `True` if current installed version is `0.2.3` but latest version is `0.2.30` or `0.2.3a`. An improvement would be to programmatically get the latest version and do a direct comparison.


------------
    
    
## Answer \#6

 Vote: 2

Created at 2019-10-31 20:34:25

------------

It's not hard to write a simple script yourself by [querying the PyPI API](https://warehouse.pypa.io/api-reference/json/). With the latest Python 3.8, it's possible using only the standard library (when using Python 3.7 or older, you'll have to install the [importlib_metadata](https://pypi.org/project/importlib-metadata/) backport):

```
# check_version.py

import json
import urllib.request
import sys

try:
    from importlib.metadata import version
except ImportError:
    from importlib_metadata import version

from distutils.version import LooseVersion


if __name__ == '__main__':
    name = sys.argv[1]
    installed_version = LooseVersion(version(name))

    # fetch package metadata from PyPI
    pypi_url = f'https://pypi.org/pypi/{name}/json'
    response = urllib.request.urlopen(pypi_url).read().decode()
    latest_version = max(LooseVersion(s) for s in json.loads(response)['releases'].keys())

    print('package:', name, 'installed:', installed_version, 'latest:', latest_version)
```


Usage example:

```
$ python check_version.py setuptools
package: setuptools installed: 41.2.0 latest: 41.6.0
```


If you're happen to have [packaging](https://packaging.pypa.io/en/latest/) installed, it's a better alternative to `distutils.version` for version parsing:

```
from distutils.version import LooseVersion

...

LooseVersion(s)
```


becomes

```
from packaging.version import parse

...

parse(s)
```



------------
    
    
## Answer \#7

 Vote: 0

Created at 2021-03-31 10:53:32

------------

Based on the accepted answer you can do:
```
pip list --outdated | grep name_of_package
```



------------
    
    
## Answer \#8

 Vote: -1

Created at 2022-12-15 19:48:06

------------

For `pydicom` these work fine,
```
import pydicom
print(pydicom.__version__) # output is a string: 'x.y.z'
```

or
```
import pydicom
print(pydicom.__version_info__) # output is a tuple: ('x', 'y', 'z')
```



------------
    
    