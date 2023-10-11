
# Post \#65907365 [Link](https://stackoverflow.com/questions/65907365/)

## tensorflow: Not creating XLA devices, tf_xla_enable_xla_devices not set

**Vote**: 15 (318/702) **Views**: 26048 (255/702) 

**Internal ID** \#1-3-267

Created at 2021-01-26 18:43:01

Tags: `python` `tensorflow`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I run drive.py program from [Code Project | A Complete guide to self driving car](https://www.codeproject.com/Articles/1273179/A-Complete-guide-to-self-driving-Car?fbclid=IwAR1L7mOaPTZ7-vwWicXcAS7FLhVLcU9BUgaivhK0P9tbXAGe0zOThAuktMs)
but when i start program i have error:
Not creating XLA devices, tf_xla_enable_xla_devices not set
Does anyone know how I can fix this problem? What should I download or reinstall?
I use:
Python 3.8.7
CUDA 11.0
tensorflow 2.4.1
On [http://0.0.0.0:4567/](http://0.0.0.0:4567/) of course I see nothing
[](https://i.stack.imgur.com/3pXqE.jpg)


----------
        
## Answer \#0

**Accepted** Vote: 29

Created at 2021-02-03 13:04:09

------------

Please add the following  of your script:
```
os.environ['TF_XLA_FLAGS'] = '--tf_xla_enable_xla_devices'
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2021-04-29 13:58:38

------------

Alternatively, one can set the environment variable in Windows system settings: `TF_XLA_FLAGS = --tf_xla_enable_xla_devices`.
According to [this discussion](https://github.com/tensorflow/tensorflow/issues/44683), this helps use the GPU much more efficiently than simply ignoring the info.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-01-27 08:34:46

------------

Usually this message should not interfere.
Please try
```
import tensorflow as tf
print("GPUs: ", len(tf.config.experimental.list_physical_devices('GPU')))
```

to check that they are detected.
If you need it, please check the release notes at github:
> XLA:CPU and XLA:GPU devices are no longer registered by default. Use TF_XLA_FLAGS=--tf_xla_enable_xla_devices if you really need them, but this flag will eventually be removed in subsequent releases.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2021-10-13 07:57:34

------------

I'm not sure that Tensorflow's XLA:CPU and XLA:GPU would be "removed in subsequent releases."  XLA:CPU is described as providing access to the CPU while the GPU is executing the code for modeling Tensorflow.  The XLA functionality, when its environmental variable is set, allows the CPU to "help out" at specific, critical performance stages and thus to accelerate the processes one might usually think the GPU is needed for.  This is explained in [https://www.tensorflow.org/xla](https://www.tensorflow.org/xla).
I had not found really clear instructions on how to set the required environmental variables in R or, more specifically, Rstudio.  In another blog, an individual suggested (a) adding an environmental path variable to open up XLA in general, but then another one specifically for use by Tensorflow.
Setting up the right environmental variable(s) may vary by operating system, scripting program (Python vs R vs Windows Ubuntu), and which CUDA and CUDNN versions are installed.
The most important thing for me was to look at the output for the specific environmental variable which was not set and thus caused XLA to not activate.  But also, XLA may need to be activated in the entire R session or script.
In my case, using Ubuntu 20.04 to run Rstudio 2021.09.0 with an NVIDIA GPU and driver version 470.57.02, CUDA-11.4 and CUDNN8_8.2.4.15 (which goes with CUDA 11.4), the following two R Setenv() commands caused my own "XLA error" to go away within the output from my Rstudio script:
```
Sys.setenv("XLA_FLAGS=--xla_gpu_cuda_data_dir"="/usr/local/cuda-11.4")

Sys.setenv("TF_XLA_FLAGS"="--tf_xla_enable_xla_devices")
```

Notice that both environment variables are set and the directory for `xla_gpu_cuda_data` is correctly named in `/usr/local/cuda-11.X`.
In addition, the environmental PATH and LD_LIBRARY_PATH variables as set in the user's profile script(s) (mine is `.profile`) upon reboot (or `source` command in the terminal) need to also indicate the correct cuda directories.


------------
    
    