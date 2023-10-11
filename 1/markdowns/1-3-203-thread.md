
# Post \#70240506 [Link](https://stackoverflow.com/questions/70240506/)

## Why is numpy native on M1 Max greatly slower than on old Intel i5?

**Vote**: 14 (336/702) **Views**: 10662 (410/702) 

**Internal ID** \#1-3-203

Created at 2021-12-06 03:16:05

Tags: `python` `numpy` `tensorflow` `anaconda` `apple-m1`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I just got my new MacBook Pro with M1 Max chip and am setting up Python. I've tried several combinational settings to test speed - now I'm quite confused. First put my questions here:
- - - - 
Evidence supporting my questions is as follows:

---


Here are the settings I've tried:

- [Miniforge-arm64](https://github.com/conda-forge/miniforge)`Kind``Apple`- [Anaconda](https://docs.anaconda.com/anaconda/install/mac-os/)`Kind``Intel`

- `conda install numpy`- 
```
conda install -c apple tensorflow-deps
python -m pip install tensorflow-macos
python -m pip install tensorflow-metal
```


- - [Apple Silicon version](https://www.jetbrains.com/pycharm/download/download-thanks.html?platform=macM1)

---


Here is the test code:
```
import time
import numpy as np
np.random.seed(42)
a = np.random.uniform(size=(300, 300))
runtimes = 10

timecosts = []
for _ in range(runtimes):
    s_time = time.time()
    for i in range(100):
        a += 1
        np.linalg.svd(a)
    timecosts.append(time.time() - s_time)

print(f'mean of {runtimes} runs: {np.mean(timecosts):.5f}s')
```

and here are the results:
```
+-----------------------------------+-----------------------+--------------------+
|   Python installed by (run on)→   | Miniforge (native M1) | Anaconda (Rosseta) |
+----------------------+------------+------------+----------+----------+---------+
| Numpy installed by ↓ | Run from → |  Terminal  |  PyCharm | Terminal | PyCharm |
+----------------------+------------+------------+----------+----------+---------+
|          Apple Tensorflow         |   4.19151  |  4.86248 |     /    |    /    |
+-----------------------------------+------------+----------+----------+---------+
|        conda install numpy        |   4.29386  |  4.98370 |  4.10029 | 4.99271 |
+-----------------------------------+------------+----------+----------+---------+
```

This is quite slow. For comparison,
- `2.39917s`- [post (but not in English)](https://www.zhihu.com/question/431073191/answer/1657202944)`2.53214s``1.00613s`- 
Here is the CPU information details:
- 
```
$ sysctl -a | grep -e brand_string -e cpu.core_count
machdep.cpu.brand_string: Intel(R) Core(TM) i5-6360U CPU @ 2.00GHz
machdep.cpu.core_count: 2
```

- 
```
% sysctl -a | grep -e brand_string -e cpu.core_count
machdep.cpu.brand_string: Apple M1 Max
machdep.cpu.core_count: 10
```


---


I follow instructions strictly from tutorials - but why would all these happen? Is it because of my installation flaws, or because of M1 Max chip? Since my work relies heavily on local runs, local speed is very important to me. Any suggestions to possible solution, or any data points on your own device would be greatly appreciated :)


----------
        
## Answer \#0

**Accepted** Vote: 15

Created at 2021-12-07 04:54:32

------------

Update Mar 28 2022: Please see @AndrejHribernik's comment below.

---


How to install numpy on M1 Max, with the most accelerated performance (Apple's vecLib)? Here's the answer as of Dec 6 2021.

---



## Steps



### I. Install miniforge


So that your Python is run natively on arm64, not translated via Rosseta.

1. Download Miniforge3-MacOSX-arm64.sh, then
2. Run the script, then open another shell


```
$ bash Miniforge3-MacOSX-arm64.sh
```


1. Create an environment (here I use name np_veclib)


```
$ conda create -n np_veclib python=3.9
$ conda activate np_veclib
```


### II. Install Numpy with BLAS interface specified as vecLib



1. To compile numpy, first need to install cython and pybind11:


```
$ conda install cython pybind11
```


1. Compile numpy by (Thanks @Marijn's answer) - don't use conda install!


```
$ pip install --no-binary :all: --no-use-pep517 numpy
```


1. An alternative of 2. is to build from source


```
$ git clone https://github.com/numpy/numpy
$ cd numpy
$ cp site.cfg.example site.cfg
$ nano site.cfg
```

Edit the copied `site.cfg`: add the following lines:
```
[accelerate]
libraries = Accelerate, vecLib
```

Then build and install:
```
$ NPY_LAPACK_ORDER=accelerate python setup.py build
$ python setup.py install
```


1. After either 2 or 3, now test whether numpy is using vecLib:


```
>>> import numpy
>>> numpy.show_config()
```

Then, info like `/System/Library/Frameworks/vecLib.framework/Headers` should be printed.

### III. For further installing other packages using conda


Make conda recognize packages installed by pip
```
conda config --set pip_interop_enabled true
```

This must be done, otherwise if e.g. `conda install pandas`, then `numpy` will be in `The following packages will be installed` list and installed again. But the new installed one is from `conda-forge` channel and is slow.

---



## Comparisons to other installations:



### 1. Competitors:


Except for the above optimal one, I also tried several other installations
- `np_default``conda create -n np_default python=3.9 numpy`- `np_openblas``conda create -n np_openblas python=3.9 numpy blas=*=*openblas*`- `np_netlib``conda create -n np_netlib python=3.9 numpy blas=*=*netlib*`
The above ABC options are directly installed from conda-forge channel. `numpy.show_config()` will show identical results. To see the difference, examine by `conda list` - e.g. `openblas` packages are installed in B. Note that `mkl` or `blis` is not supported on arm64.
- `np_openblas_source``brew install openblas``[openblas]``/opt/homebrew/opt/openblas``site.cfg`- `M1``i9–9880H`[post](https://towardsdatascience.com/m1-macbook-pro-vs-intel-i9-macbook-pro-ultimate-data-science-comparison-dde8fc32b5df)- `i5-6360U`

### 2. Benchmarks:


Here I use two benchmarks:

1. mysvd.py: My SVD decomposition


```
import time
import numpy as np
np.random.seed(42)
a = np.random.uniform(size=(300, 300))
runtimes = 10

timecosts = []
for _ in range(runtimes):
    s_time = time.time()
    for i in range(100):
        a += 1
        np.linalg.svd(a)
    timecosts.append(time.time() - s_time)

print(f'mean of {runtimes} runs: {np.mean(timecosts):.5f}s')
```


1. dario.py: A benchmark script by Dario Radečić at the post above.



### 3. Results:


```
+-------+-----------+------------+-------------+-----------+--------------------+----+----------+----------+
|  sec  | np_veclib | np_default | np_openblas | np_netlib | np_openblas_source | M1 | i9–9880H | i5-6360U |
+-------+-----------+------------+-------------+-----------+--------------------+----+----------+----------+
| mysvd |  1.02300  |   4.29386  |   4.13854   |  4.75812  |      12.57879      |  / |     /    |  2.39917 |
+-------+-----------+------------+-------------+-----------+--------------------+----+----------+----------+
| dario |     21    |     41     |      39     |    323    |         40         | 33 |    23    |    78    |
+-------+-----------+------------+-------------+-----------+--------------------+----+----------+----------+
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2021-12-06 05:53:24

------------


## Possible Cause: Different BLAS Libraries


Since the benchmark is running linear algebra routines, what is likely being tested here are the BLAS implementations. A default Anaconda distribution for  platform is going to come with Intel's MKL implementation; the  platform only has the generic Netlib BLAS and the OpenBLAS implementation options.
For me (MacOS w/ Intel i9), I get the following benchmark results:

| BLAS Implmentation | Mean Timing (s) | 
| ------------------ | --------------- | 
| mkl | 0.95932 | 
| blis | 1.72059 | 
| openblas | 2.17023 | 
| netlib | 5.72782 | 


So, I suspect the old MBP had MKL installed, and the M1 system is installing either Netlib or OpenBLAS. Maybe try figuring out whether Netlib or OpenBLAS are faster on M1, and keep the faster one.

---



### Specifying BLAS Implementation


Here are specifically the different environments I tested:
```
# MKL
conda create -n np_mkl python=3.9 numpy blas=*=*mkl*

# BLIS
conda create -n np_blis python=3.9 numpy blas=*=*blis*

# OpenBLAS
conda create -n np_openblas python=3.9 numpy blas=*=*openblas*

# Netlib
conda create -n np_netlib python=3.9 numpy blas=*=*netlib*
```

and ran the benchmark script (`so-np-bench.py`) with
```
conda run -n np_mkl python so-np-bench.py

# etc.
```



------------
    
    
## Answer \#2

 Vote: 7

Created at 2022-06-06 12:30:56

------------

With [Miniforge3-MacOSX-arm64](https://github.com/conda-forge/miniforge), and `conda install -c conda-forge numpy "libblas=*=*accelerate"`, it works perfectly on my Macbook M1 Max.
- - 


------------
    
    
## Answer \#3

 Vote: 1

Created at 2022-11-13 09:57:26

------------

Thank you for the tips. I followed the following in my freshly new MAC M1 MAX:

1. I installed the Minoforge3 (bash Miniforge3-MacOSX-arm64.sh)
2. Initialized a conda base environment (conda init) with Python 3.10
3. Installed numpy as: conda install numpy "libblas=*=*accelerate"


And then the suggested benchmarks from the link:

1. The script mention above mysvd.py runs in mean of 10 runs: 1.08088s
2. The script dario.py from https://gist.githubusercontent.com/daradecic/a2ac0a75d7e5f22c9aa07174dcbbe061/raw/a56ee217e6d3f949b1d1f719a7a134cef130cd9f/macs.py gives:


```
Dotted two 4096x4096 matrices in 0.28 s.
Dotted two vectors of length 524288 in 0.11 ms.
SVD of a 2048x1024 matrix in 0.44 s.
Cholesky decomposition of a 2048x2048 matrix in 0.07 s.
Eigendecomposition of a 2048x2048 matrix in 3.83 s.

TOTAL TIME = 19 seconds
```



------------
    
    