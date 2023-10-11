
# Post \#55224016 [Link](https://stackoverflow.com/questions/55224016/)

## ImportError: libcublas.so.10.0: cannot open shared object file: No such file or directory

**Vote**: 59 (107/702) **Views**: 172312 (45/702) 

**Internal ID** \#1-3-228

Created at 2019-03-18 14:46:31

Tags: `tensorflow` `ubuntu-18.04`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I have installed Cuda 10.1 and cudnn on Ubuntu 18.04 and it seems to be installed properly as type nvcc and nvidia-smi, I get proper response:
```
user:~$ nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2019 NVIDIA Corporation
Built on Fri_Feb__8_19:08:17_PST_2019
Cuda compilation tools, release 10.1, V10.1.105
user:~$ nvidia-smi 
Mon Mar 18 14:36:47 2019       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.43       Driver Version: 418.43       CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Quadro K5200        Off  | 00000000:03:00.0  On |                  Off |
| 26%   39C    P8    14W / 150W |    225MiB /  8118MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1538      G   /usr/lib/xorg/Xorg                            32MiB |
|    0      1583      G   /usr/bin/gnome-shell                           5MiB |
|    0      3008      G   /usr/lib/xorg/Xorg                           100MiB |
|    0      3120      G   /usr/bin/gnome-shell                          82MiB |
+-----------------------------------------------------------------------------+
```

I have installed tensorflow using:
`user:~$ sudo pip3 install --upgrade tensorflow-gpu`
```
The directory '/home/amin/.cache/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/home/amin/.cache/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Requirement already up-to-date: tensorflow-gpu in /usr/local/lib/python3.6/dist-packages (1.13.1)
Requirement already satisfied, skipping upgrade: keras-applications>=1.0.6 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (1.0.7)
Requirement already satisfied, skipping upgrade: protobuf>=3.6.1 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (3.6.1)
Requirement already satisfied, skipping upgrade: wheel>=0.26 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (0.32.3)
Requirement already satisfied, skipping upgrade: absl-py>=0.1.6 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (0.7.0)
Requirement already satisfied, skipping upgrade: keras-preprocessing>=1.0.5 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (1.0.9)
Requirement already satisfied, skipping upgrade: gast>=0.2.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (0.2.2)
Requirement already satisfied, skipping upgrade: termcolor>=1.1.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (1.1.0)
Requirement already satisfied, skipping upgrade: grpcio>=1.8.6 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (1.18.0)
Requirement already satisfied, skipping upgrade: tensorflow-estimator<1.14.0rc0,>=1.13.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (1.13.0)
Requirement already satisfied, skipping upgrade: six>=1.10.0 in /usr/lib/python3/dist-packages (from tensorflow-gpu) (1.11.0)
Requirement already satisfied, skipping upgrade: numpy>=1.13.3 in /usr/lib/python3/dist-packages (from tensorflow-gpu) (1.13.3)
Requirement already satisfied, skipping upgrade: astor>=0.6.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (0.7.1)
Requirement already satisfied, skipping upgrade: tensorboard<1.14.0,>=1.13.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-gpu) (1.13.1)
Requirement already satisfied, skipping upgrade: h5py in /usr/local/lib/python3.6/dist-packages (from keras-applications>=1.0.6->tensorflow-gpu) (2.9.0)
Requirement already satisfied, skipping upgrade: setuptools in /usr/local/lib/python3.6/dist-packages (from protobuf>=3.6.1->tensorflow-gpu) (40.6.3)
Requirement already satisfied, skipping upgrade: mock>=2.0.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-estimator<1.14.0rc0,>=1.13.0->tensorflow-gpu) (2.0.0)
Requirement already satisfied, skipping upgrade: werkzeug>=0.11.15 in /usr/local/lib/python3.6/dist-packages (from tensorboard<1.14.0,>=1.13.0->tensorflow-gpu) (0.14.1)
Requirement already satisfied, skipping upgrade: markdown>=2.6.8 in /usr/local/lib/python3.6/dist-packages (from tensorboard<1.14.0,>=1.13.0->tensorflow-gpu) (3.0.1)
Requirement already satisfied, skipping upgrade: pbr>=0.11 in /usr/local/lib/python3.6/dist-packages (from mock>=2.0.0->tensorflow-estimator<1.14.0rc0,>=1.13.0->tensorflow-gpu) (5.1.1)
```

However when I am trying to import tensorflow I am getting error about  libcublas.so.10.0:
```
user:~$ python3
Python 3.6.7 (default, Oct 22 2018, 11:32:17) 
[GCC 8.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow as tf
Traceback (most recent call last):
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow.py", line 58, in <module>
    from tensorflow.python.pywrap_tensorflow_internal import *
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow_internal.py", line 28, in <module>
    _pywrap_tensorflow_internal = swig_import_helper()
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow_internal.py", line 24, in swig_import_helper
    _mod = imp.load_module('_pywrap_tensorflow_internal', fp, pathname, description)
  File "/usr/lib/python3.6/imp.py", line 243, in load_module
    return load_dynamic(name, filename, file)
  File "/usr/lib/python3.6/imp.py", line 343, in load_dynamic
    return _load(spec)
ImportError: libcublas.so.10.0: cannot open shared object file: No such file or directory

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/__init__.py", line 24, in <module>
    from tensorflow.python import pywrap_tensorflow  # pylint: disable=unused-import
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/__init__.py", line 49, in <module>
    from tensorflow.python import pywrap_tensorflow
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow.py", line 74, in <module>
    raise ImportError(msg)
ImportError: Traceback (most recent call last):
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow.py", line 58, in <module>
    from tensorflow.python.pywrap_tensorflow_internal import *
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow_internal.py", line 28, in <module>
    _pywrap_tensorflow_internal = swig_import_helper()
  File "/usr/local/lib/python3.6/dist-packages/tensorflow/python/pywrap_tensorflow_internal.py", line 24, in swig_import_helper
    _mod = imp.load_module('_pywrap_tensorflow_internal', fp, pathname, description)
  File "/usr/lib/python3.6/imp.py", line 243, in load_module
    return load_dynamic(name, filename, file)
  File "/usr/lib/python3.6/imp.py", line 343, in load_dynamic
    return _load(spec)
ImportError: libcublas.so.10.0: cannot open shared object file: No such file or directory


Failed to load the native TensorFlow runtime.

See https://www.tensorflow.org/install/errors

for some common reasons and solutions.  Include the entire stack trace
above this error message when asking for help.
```

What I am missing? and How can I resolve this?
Thanks


----------
        
## Answer \#0

**Accepted** Vote: 52

Created at 2019-03-19 10:17:21

------------

I downloaded cuda 10.0 from the following link
[CUDA 10.0](https://developer.nvidia.com/cuda-10.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=debnetwork)

Then I installed it using the following commands:

```
sudo dpkg -i cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo apt-get update
sudo apt-get install cuda-10-0
```


I then installed cudnn v7.5.0 for CUDA 10.0 by going to link 
[CUDNN download](https://developer.nvidia.com/rdp/cudnn-download) and you need to logon using an account.

and after choosing the correct version I downloaded via link [CUDNN power link](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/v7.5.0.56/prod/10.0_20190219/cudnn-10.0-linux-ppc64le-v7.5.0.56.tgz)
after that I added the include and lib files for cudnn as follows:

```
sudo cp -P cuda/targets/ppc64le-linux/include/cudnn.h /usr/local/cuda-10.0/include/
sudo cp -P cuda/targets/ppc64le-linux/lib/libcudnn* /usr/local/cuda-10.0/lib64/
sudo chmod a+r /usr/local/cuda-10.0/lib64/libcudnn*
```


After modified the .bashrc for lib and path of cuda 10.0, if you do not have it you need to add them into .bashrc

```
export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64:${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```


And after all these steps, I managed to import tensorflow in python3 successfully.


------------
    
    
## Answer \#1

 Vote: 40

Created at 2020-10-21 22:05:46

------------

If using Cuda 10.1 (as directed in [https://www.tensorflow.org/install/gpu](https://www.tensorflow.org/install/gpu)), the problem is that libcublas.so.10 was moved out of the cuda-10.1 directory and into cuda-10.2(!)
Copying from this answer: [https://github.com/tensorflow/tensorflow/issues/26182#issuecomment-684993950](https://github.com/tensorflow/tensorflow/issues/26182#issuecomment-684993950)
> ... libcublas.so.10 sits in /usr/local/cuda-10.2/lib64 (surprise from nvidia - installation of 10.1 installs some 10.2 stuff) but only /usr/local/cuda is in include path which points to /usr/local/cuda-10.1.
The  is to add it to your include path:
```
export LD_LIBRARY_PATH=/usr/local/cuda-10.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Note: This fix is known to work in Cuda 10.1, V10.1.243 (print your version with `nvcc -V`).


------------
    
    
## Answer \#2

 Vote: 16

Created at 2020-02-12 09:58:30

------------

CUDA 10.1 (installed as per tensorflow docs) throws `can't find libcublas.so.10.0` errors. The libs exist in `/usr/local/cuda-10.1/targets/x86_64-linux/lib/` but are misnamed. 

There was another (lost) stackoverflow post saying this was a pinned dependency issue with the package and could be fixed with an extra cli flag to apt. This didn't seem to fix the issue for me.

Tested workaround is to modify instructions to downgrade to CUDA 10.0

```
# Uninstall packages from tensorflow installation instructions 
sudo apt-get remove cuda-10-1 \
    libcudnn7 \
    libcudnn7-dev \
    libnvinfer6 \
    libnvinfer-dev \
    libnvinfer-plugin6

# WORKS: Downgrade to CUDA-10.0
sudo apt-get install -y --no-install-recommends \
    cuda-10-0 \
    libcudnn7=7.6.4.38-1+cuda10.0  \
    libcudnn7-dev=7.6.4.38-1+cuda10.0;
sudo apt-get install -y --no-install-recommends \
    libnvinfer6=6.0.1-1+cuda10.0 \
    libnvinfer-dev=6.0.1-1+cuda10.0 \
    libnvinfer-plugin6=6.0.1-1+cuda10.0;
```


Upgrading to CUDA-10.2 also seems to suffer from the same problem

```
# BROKEN: Upgrade to CUDA-10.2 
# use `apt show -a libcudnn7 libnvinfer7` to find 10.2 compatable version numbers
sudo apt-get install -y --no-install-recommends \
    cuda-10-2 \
    libcudnn7=7.6.5.32-1+cuda10.2  \
    libcudnn7-dev=7.6.5.32-1+cuda10.2;
sudo apt-get install -y --no-install-recommends \
    libnvinfer7=7.0.0-1+cuda10.2 \
    libnvinfer-dev=7.0.0-1+cuda10.2 \
    libnvinfer-plugin7=7.0.0-1+cuda10.2;
```


Test GPU Visibility in Python

```
python3
>>> import tensorflow as tf
>>> tf.test.is_gpu_available()
```


FutureWarnings on tensorflow import

[https://github.com/tensorflow/tensorflow/issues/30427](https://github.com/tensorflow/tensorflow/issues/30427)

two solutions:

- `pip3 install tf-nightly-gpu`- `pip3 install "numpy<1.17"`



You also need the correct tensorflow version to match with your CUDA version

Tensorflow / CUDA version combinations:

- - - 

See for the full list: [https://www.tensorflow.org/install/source#tested_build_configurations](https://www.tensorflow.org/install/source#tested_build_configurations)

You may potentually need to reinstall tensorflow with a named version matching your CUDA

```
pip uninstall tensorflow tensorflow-gpu
pip install tensorflow==2.1.0 tensorflow-gpu==2.1.0
```


Then add CUDA to $PATH and $LD_LIBRARY_PATH in ~/.bashrc

~/.bashrc

```
# CUDA Environment Setup: https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#environment-setup
for CUDA_BIN_DIR in `find /usr/local/cuda-*/bin   -maxdepth 0`; do export PATH="$PATH:$CUDA_BIN_DIR"; done;
for CUDA_LIB_DIR in `find /usr/local/cuda-*/lib64 -maxdepth 0`; do export LD_LIBRARY_PATH="${LD_LIBRARY_PATH:+${LD_LIBRARY_PATH}:}$CUDA_LIB_DIR"; done;

export            PATH=`echo $PATH            | tr ':' '\n' | awk '!x[$0]++' | tr '\n' ':' | sed 's/:$//g'` # Deduplicate $PATH
export LD_LIBRARY_PATH=`echo $LD_LIBRARY_PATH | tr ':' '\n' | awk '!x[$0]++' | tr '\n' ':' | sed 's/:$//g'` # Deduplicate $LD_LIBRARY_PATH
```



------------
    
    
## Answer \#3

 Vote: 3

Created at 2019-04-21 11:54:16

------------

This error occurs when the version of cuda and tensorflow installed are not compatible. I encountered a similar ImportError while running tensorflow version 1.13.0 with cuda 9. Since I had installed tensorflow on a virtual environment with pip, I just uninstalled tensorflow 1.13.0 and installed tensorflow 1.12.0 as follow;

```
pip uninstall tensorflow-gpu tensorflow-estimator tensorboard
    pip install tensorflow-gpu==1.12.0
```


Everything now works.


------------
    
    
## Answer \#4

 Vote: 3

Created at 2020-10-29 11:40:14

------------

As CalderBot mentioned you can do this as well
> sudo cp -r /usr/local/cuda-10.2/lib64/libcu* /usr/local/cuda-10.1/lib64/


------------
    
    
## Answer \#5

 Vote: 3

Created at 2020-11-24 14:43:53

------------

I had the correct version of CUDA and `tensorflow-gpu==1.14.0` installed on my conda environment, but somehow I was still getting this error message. [This post](https://beerensahu.wordpress.com/2020/07/10/a-common-issue-faced-while-training-with-tensorflow-gpu-code-could-not-dlopen-library-libcudart-so-10-0-dlerror-libcudart-so-10-0/) helped me to finally solve it.
I had previously installed `tensorflow-gpu` via `pip` - after creating a new environment and installing `tensorflow-gpu` via `conda` solved my problem.
```
conda install -c anaconda tensorflow-gpu=1.14.0
```



------------
    
    
## Answer \#6

 Vote: 2

Created at 2019-06-17 06:11:47

------------

I had the same issue. I fixed it by adding the below command to the '' file.

> export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.0/lib64/



```
Ubuntu 16.04 LTS
Tensorflow GPU 2.0beta1
Cuda 10.0
cuDNN 7.6.0 for Cuda 10.0
```


I used conda to configure my system.


------------
    
    
## Answer \#7

 Vote: 2

Created at 2020-04-16 07:53:13

------------

The problem is caused by your current cuda version which is 10.1 (as we can see from the top right corner of your image).

As you can see from the official TF website, the correspondence between tf and cuda is: [TF website for the chart](https://tensorflow.google.cn/install/source)

```
Version                 cuDNN    CUDA
tensorflow-2.1.0         7.6       10.1
tensorflow-2.0.0         7.4       10.0
tensorflow_gpu-1.14.0    7.4       10.0
tensorflow_gpu-1.13.1    7.4       10.0
```


Thus, you can either upgrade your tf to 2.1 or downgrade your cuda with:

```
conda install cudatoolkit=10.0.130
```


Then it would automatically downgrade your cudnn as well.


------------
    
    
## Answer \#8

 Vote: 1

Created at 2020-10-01 09:44:27

------------

In case someone is still having this issue, libcublas.so.10 can exist but under the name libcublas.so.10.0
So, you can fix it by running:
```
sudo ln libcublas.so.10.0.130 libcublas.so.10
```

in `/usr/local/cuda-10.0/lib64`


------------
    
    
## Answer \#9

 Vote: 0

Created at 2019-05-05 07:58:42

------------

Change my tensorflow version solved my problem.

check this issue [1](https://github.com/tensorflow/tensorflow/issues/26182)[https://github.com/tensorflow/tensorflow/issues/26182](https://github.com/tensorflow/tensorflow/issues/26182))

> Official tensorflow-gpu binaries (the one downloaded by pip or conda) are built with cuda 9.0, cudnn 7 since TF 1.5, and cuda 10.0, cudnn 7 since TF 1.13. These are written in the release notes. You have to use the matching version of cuda if using the official binaries.


------------
    
    
## Answer \#10

 Vote: 0

Created at 2021-01-11 10:41:13

------------

Is your computer CUDA capable?
In linux, you can verify if your system has a CUDA-capable GPU with:
```
$ lspci | grep -i nvidia
```

If you do not see any settings, update the PCI hardware database that Linux maintains by entering update-pciids (generally found in /sbin) at the command line and rerun the previous lspci command.
In this page you have instructions to install CUDA:
[https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
If your computer is not CUDA capable, you can install another distribution of tensorflow or compile tensorflow code: [https://www.tensorflow.org/install/source](https://www.tensorflow.org/install/source)


------------
    
    
## Answer \#11

 Vote: -1

Created at 2019-07-25 01:51:49

------------

Amin,

I'm getting the same error when I try to run imagenet tutorial from tensorflow models package -- [https://github.com/tensorflow/models/tree/master/tutorials/image/imagenet](https://github.com/tensorflow/models/tree/master/tutorials/image/imagenet)

```
python3 classify_image.py
 ...
 2019-07-21 22:29:58.367858: I tensorflow/stream_executor/platform/default/dso_loader.cc:53] Could not dlopen library 'libcudart.so.10.0'; dlerror: libcudart.so.10.0: cannot open shared object file: No such file or directory
 2019-07-21 22:29:58.367982: I tensorflow/stream_executor/platform/default/dso_loader.cc:53] Could not dlopen library 'libcublas.so.10.0'; dlerror: libcublas.so.10.0: cannot open shared object file: No such file or directory
 2019-07-21 22:29:58.368112: I tensorflow/stream_executor/platform/default/dso_loader.cc:53] Could not dlopen library 'libcufft.so.10.0'; dlerror: libcufft.so.10.0: cannot open shared object file: No such file or directory
 2019-07-21 22:29:58.368234: I tensorflow/stream_executor/platform/default/dso_loader.cc:53] Could not dlopen library 'libcurand.so.10.0'; dlerror: libcurand.so.10.0: cannot open shared object file: No such file or directory
 2019-07-21 22:29:58.368369: I tensorflow/stream_executor/platform/default/dso_loader.cc:53] Could not dlopen library 'libcusolver.so.10.0'; dlerror: libcusolver.so.10.0: cannot open shared object file: No such file or directory
 2019-07-21 22:29:58.368498: I tensorflow/stream_executor/platform/default/dso_loader.cc:53] Could not dlopen library 'libcusparse.so.10.0'; dlerror: libcusparse.so.10.0: cannot open shared object file: No such file or directory
 2019-07-21 22:29:58.374333: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudnn.so.7
```


I think there's a version incompatibility somewhere and likely tensorflow, still relies on the old version of binaries provided by cuda libraries. Going to the place where binaries are stored and creating a link that's named 10.0 but either targets 10.1 or the default version of the library, seems to solve the problem for me.

```
# cd /usr/lib/x86_64-linux-gnu
 # ln -s libcudart.so.10.1 libcudart.so.10.0
 # ln -s libcublas.so libcublas.so.10.0
 # ln -s libcufft.so libcufft.so.10.0
 # ln -s libcurand.so libcurand.so.10.0
 # ln -s libcusolver.so libcusolver.so.10.0
 # ln -s libcusparse.so libcusparse.so.10.0
```


Now I'm able to run tutorial successfully

```
2019-07-24 21:43:21.172908: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudart.so.10.0
 2019-07-24 21:43:21.174653: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcublas.so.10.0
 2019-07-24 21:43:21.175826: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcufft.so.10.0
 2019-07-24 21:43:21.182305: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcurand.so.10.0
 2019-07-24 21:43:21.183970: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcusolver.so.10.0
 2019-07-24 21:43:21.206796: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcusparse.so.10.0
 2019-07-24 21:43:21.210685: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudnn.so.7
 2019-07-24 21:43:21.212694: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1763] Adding visible gpu devices: 0
 2019-07-24 21:43:21.213060: I tensorflow/core/platform/cpu_feature_guard.cc:142]      
 Your CPU supports instructions that this TensorFlow binary was not compiled to use: FMA
 2019-07-24 21:43:21.238541: I tensorflow/core/platform/profile_utils/cpu_utils.cc:94] CPU Frequency: 3214745000 Hz
 2019-07-24 21:43:21.240096: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x557e2b682ce0 executing computations on platform Host. Devices:
 2019-07-24 21:43:21.240162: I tensorflow/compiler/xla/service/service.cc:175]   StreamExecutor device (0): <undefined>, <undefined>
 2019-07-24 21:43:21.355158: I tensorflow/compiler/xla/service/service.cc:168] XLA service 0x557e2b652000 executing computations on platform CUDA. Devices:
 2019-07-24 21:43:21.355234: I tensorflow/compiler/xla/service/service.cc:175]   StreamExecutor device (0): GeForce GTX 1060 6GB, Compute Capability 6.1
 2019-07-24 21:43:21.357074: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1640] Found device 0 with properties: 
 name: GeForce GTX 1060 6GB major: 6 minor: 1 memoryClockRate(GHz): 1.7715
 pciBusID: 0000:01:00.0
 2019-07-24 21:43:21.357151: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudart.so.10.0
 2019-07-24 21:43:21.357207: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcublas.so.10.0
 2019-07-24 21:43:21.357245: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcufft.so.10.0
 2019-07-24 21:43:21.357283: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcurand.so.10.0
 2019-07-24 21:43:21.357321: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcusolver.so.10.0
 2019-07-24 21:43:21.357358: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcusparse.so.10.0
 2019-07-24 21:43:21.357395: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudnn.so.7
 2019-07-24 21:43:21.360449: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1763] Adding visible gpu devices: 0
 2019-07-24 21:43:21.380616: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudart.so.10.0
 2019-07-24 21:43:21.385223: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1181] Device interconnect StreamExecutor with strength 1 edge matrix:
 2019-07-24 21:43:21.385272: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1187]      0 
 2019-07-24 21:43:21.385299: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1200] 0:   N 
 2019-07-24 21:43:21.388647: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1326] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 5250 MB memory) -> physical GPU (device: 0, name: GeForce GTX 1060 6GB, pci bus id: 0000:01:00.0, compute capability: 6.1)
 2019-07-24 21:43:32.001598: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcublas.so.10.0
 2019-07-24 21:43:32.532105: I tensorflow/stream_executor/platform/default/dso_loader.cc:42] Successfully opened dynamic library libcudnn.so.7
 W0724 21:43:34.981204 140284114071872 deprecation_wrapper.py:119] From classify_image.py:85: The name tf.gfile.GFile is deprecated. Please use tf.io.gfile.GFile instead.
```



------------
    
    
## Answer \#12

 Vote: -1

Created at 2020-10-30 16:42:37

------------

I faced similar issue when trying to install [spconv](https://github.com/traveller59/spconv).
```
File "/home/kmario23/anaconda3/envs/py38/lib/python3.8/site-packages/torch/_ops.py", line 105, in load_library
    ctypes.CDLL(path)
  File "/home/kmario23/anaconda3/envs/py38/lib/python3.8/ctypes/__init__.py", line 373, in __init__
    self._handle = _dlopen(self._name, mode)
OSError: libcublas.so.10: cannot open shared object file: No such file or directory
```

Installing the cuda toolkit version `10.1` inside the specific environment resolved the issue:
```
$ conda install -c anaconda cudatoolkit=10.1
```



------------
    
    