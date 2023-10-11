
# Post \#67929517 [Link](https://stackoverflow.com/questions/67929517/)

## Conda command to list size of packages

**Vote**: 8 (428/702) **Views**: 4977 (522/702) 

**Internal ID** \#1-3-196

Created at 2021-06-10 23:21:13

Tags: `anaconda` `conda`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I would like to find out the size of conda packages to delete the huge and seldom used ones. Which conda command should I use to find out the package size?
`conda list` will list the packages but does not show the package size.
I welcome other methods to find out package size.
I'm using Windows 10.


----------
        
## Answer \#0

**Accepted** Vote: 8

Created at 2021-06-14 19:55:27

------------


# Mine the conda-meta


One way to get at this is by mining the JSON metadata files for each package inside an environment's `conda-meta/` directory. There are two types of sizes listed:
- - 
Since you seem interested in total size of a package, let's do the simpler . This will let us get a quick ranking of packages by their download size.

```
## activate the environment of interest
conda activate foo

## search all the JSONs for '"size":'
grep '"size":' ${CONDA_PREFIX}/conda-meta/*.json |\ 

  ## sort result
  sort -k3rn |\

  ## show only filename
  sed 's/.*conda-meta\///g' |\

  ## print with columns
  column -t
```


```
jaxlib-0.1.67-py39h6e9494a_0.json:          "size":  38576847,
scipy-1.6.3-py39h056f1c0_0.json:            "size":  19495906,
python-3.9.4-h9133fd0_0_cpython.json:       "size":  13160553,
libopenblas-0.3.15-openmp_h5e1b9a4_1.json:  "size":  9163719,
numpy-1.20.3-py39h7eed0ac_1.json:           "size":  5732039,
tk-8.6.10-hb0a8c7a_1.json:                  "size":  3420669,
openssl-1.1.1k-h0d85af4_0.json:             "size":  1985060,
sqlite-3.35.5-h44b9ce1_0.json:              "size":  1810221,
libgfortran5-9.3.0-h6c81a4c_22.json:        "size":  1766473,
pip-21.1.2-pyhd8ed1ab_0.json:               "size":  1147500,
libcxx-11.1.0-habf9029_0.json:              "size":  1055976,
setuptools-49.6.0-py39h6e9494a_3.json:      "size":  972968,
ncurses-6.2-h2e338ed_4.json:                "size":  901840,
jax-0.2.14-pyhd8ed1ab_0.json:               "size":  571585,
llvm-openmp-11.1.0-hda6cdc1_1.json:         "size":  274368,
readline-8.1-h05e3726_0.json:               "size":  272444,
xz-5.2.5-haf1e3a3_1.json:                   "size":  233058,
certifi-2021.5.30-py39h6e9494a_0.json:      "size":  144599,
ca-certificates-2021.5.30-h033912b_0.json:  "size":  139088,
tzdata-2021a-he74cb21_0.json:               "size":  123802,
zlib-1.2.11-h7795811_1010.json:             "size":  104180,
absl-py-0.12.0-pyhd8ed1ab_0.json:           "size":  98565,
tqdm-4.61.0-pyhd8ed1ab_0.json:              "size":  81513,
opt_einsum-3.3.0-pyhd8ed1ab_1.json:         "size":  54494,
libffi-3.3-h046ec9c_2.json:                 "size":  46425,
wheel-0.36.2-pyhd3deb0d_0.json:             "size":  31381,
python-flatbuffers-2.0-pyhd8ed1ab_0.json:   "size":  28606,
libgfortran-5.0.0-9_3_0_h6c81a4c_22.json:   "size":  19280,
six-1.16.0-pyh6c4a22f_0.json:               "size":  14259,
libblas-3.9.0-9_openblas.json:              "size":  11762,
libcblas-3.9.0-9_openblas.json:             "size":  11671,
liblapack-3.9.0-9_openblas.json:            "size":  11671,
python_abi-3.9-1_cp39.json:                 "size":  3921,
```

The above output shows `jaxlib` is the largest package, followed by `scipy`, then the `python` interpreter itself. In this case, if I wanted to remove `jaxlib` it would also entail removing `jax`.

### Notes


I think the above serves as a first approximation for ranking the packages by size. The `size_in_bytes` could possibly be more exact, but to be thorough one would need to also consider which individual files are hardlinked, since those actually shouldn't be counted against the package on a  level. For them, there is only one copy per system and it gets reused across environments.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-06-14 06:13:25

------------

If you are concerned about using up disk space, you can run this command to clean up all the temporary packages, zip files, etc that conda used to setup your environment.
```
conda clean --all
```

These files remain and can clutter up your disk over time.
use
```
du -h -s $(conda info --base)/envs/test-1
```

for the file size of the package


------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-06-14 21:16:36

------------

You can use `pip` module through `subprocess` communication (Python >= 3.7):
```
from pip._internal.commands.show import search_packages_info
import subprocess
import sys
import json
import pathlib
import math
import operator

TMPL = '{:<30} {:<30} {:<10} {:>9}'


def humanize(size):
    """Convert size in bytes to human readable output."""
    try:
        units = ['B', 'KB', 'MB', 'GB']
        i = math.floor(math.log(size) / math.log(1024))
        hsize = f"{round(size / 1024**i)}{units[i]}"
    except ValueError:
        hsize = '0B'
    return hsize


def pip_list_ext():
    """List installed packages with additional information."""
    # python -m pip list --format --json --verbose
    pip_list = subprocess.run([sys.executable, '-m', 'pip', 'list',
                               '--format', 'json', '--verbose'],
                              capture_output=True)

    if pip_list.returncode != 0:
        raise RuntimeError('...')

    query = [pkg['name'] for pkg in json.loads(pip_list.stdout)]
    packages = []
    for pkg in search_packages_info(query):
        location = pathlib.Path(pkg['location'])
        pkg['installer'] = pkg.get('installer', '')
        pkg['files'] = pkg.get('files', [])
        size = sum((location / f).stat().st_size
                       for f in pkg['files'] if (location / f).is_file())
        pkg['size'] = size
        packages.append(pkg)

    return packages

if __name__ == '__main__':
    packages = pip_list_ext()

    print(TMPL.format('Package', 'Version', 'Installer', 'Size'))
    print(TMPL.format('-'*30, '-'*30, '-'*10, '-'*9))
    for pkg in packages:
        print(TMPL.format(pkg['name'], pkg['version'],
                          pkg['installer'], humanize(pkg['size'])))

    # TOP 10
    N = 10
    pkgs = sorted(packages, key=operator.itemgetter('size'), reverse=True)
    print(f"\nTOP {N}:")
    for i, pkg in enumerate(pkgs[:N], 1):
        print(f"{i:>2}. {pkg['name']} ({humanize(pkg['size'])})")
```

Sample output
```
Package                        Version                        Installer       Size
------------------------------ ------------------------------ ---------- ---------
appnope                        0.1.2                          conda           13KB
backcall                       0.2.0                          conda           58KB
certifi                        2021.5.30                                     261KB
cffi                           1.14.5                         conda          780KB
cryptography                   3.4.7                          conda            2MB
decorator                      5.0.9                          conda           35KB
ipython                        7.22.0                         conda            4MB
ipython-genutils               0.2.0                          conda           66KB
jedi                           0.17.2                         conda            5MB
mkl-fft                        1.3.0                                            0B
mkl-random                     1.2.1                          conda            1MB
mkl-service                    2.3.0                                            0B
numpy                          1.20.2                         conda           23MB
pandas                         1.2.4                                          27MB
parso                          0.7.0                          conda          320KB
pexpect                        4.8.0                          conda          177KB
pickleshare                    0.7.5                          conda           23KB
pip                            21.1.2                                           0B
prompt-toolkit                 3.0.17                         conda            2MB
ptyprocess                     0.7.0                          conda           40KB
pycparser                      2.20                           conda          547KB
Pygments                       2.9.0                          conda            6MB
pyOpenSSL                      20.0.1                         conda          212KB
python-dateutil                2.8.1                          conda          619KB
pytz                           2021.1                         conda          971KB
setuptools                     52.0.0.post20210125                              0B
six                            1.15.0                         conda           63KB
traitlets                      5.0.5                          conda          798KB
wcwidth                        0.2.5                          conda          411KB
wheel                          0.36.2                                           0B
```

```
TOP 10:
 1. pandas (27MB)
 2. numpy (23MB)
 3. Pygments (6MB)
 4. jedi (5MB)
 5. ipython (4MB)
 6. cryptography (2MB)
 7. prompt-toolkit (2MB)
 8. mkl-random (1MB)
 9. pytz (971KB)
10. traitlets (798KB)
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2021-06-14 06:38:24

------------

The `du tool` can tell you how much disk space is occupied by a particular directory.
For example, create an identical conda environment (Miniconda) and check its disk usage like this:
```
$ conda create -n testenv -y python numpy pandas

$ du -h -s $(conda info --base)/envs/testenv
1.2G    /opt/miniconda/envs/testenv
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-06-15 13:45:42

------------

This solution works for Windows 10.
Install Windows SysInternals so that you have the command utility `du`.
Run the `du` command to find out the packages size under `site-packages` in Anaconda and store result in csv file.
```
$ du -c -v Lib/site-packages/ > packages.csv
```

Open `packages.csv` using Excel. Sort the column for the package size in descending order. Now, you have the information on the packages with the largest package size.


------------
    
    