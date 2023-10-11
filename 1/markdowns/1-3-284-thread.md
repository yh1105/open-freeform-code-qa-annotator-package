
# Post \#55970686 [Link](https://stackoverflow.com/questions/55970686/)

## Tensorboard not found as magic function in jupyter

**Vote**: 43 (145/702) **Views**: 31402 (226/702) 

**Internal ID** \#1-3-284

Created at 2019-05-03 13:20:14

Tags: `python` `tensorflow` `tensorflow2.0` `tensorboard` `tensorflow2.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I want to run tensorboard in jupyter using the latest tensorflow 2.0.0a0. 
With the tensorboard version 1.13.1, and python 3.6. 

using 

`... %tensorboard --logdir {logs_base_dir}`

I get the error :

`UsageError: Line magic function %tensorboard not found`

Do you have an idea what the problem could be? It seems that all versions are up to date and the command seems correct too. 

Thanks


----------
        
## Answer \#0

**Accepted** Vote: 79

Created at 2019-05-03 13:28:20

------------


For newer TF versions (`tensorflow>=1.14.0` & `tensorflow != 2.0.0a0` -  newer than TF2.0-alpha) load the extension like this
```
%load_ext tensorboard
```


The extension needs to be loaded first:
```
%load_ext tensorboard.notebook
%tensorboard --logdir {logs_base_dir}
```



------------
    
    
## Answer \#1

 Vote: 12

Created at 2019-10-17 21:59:49

------------

If you are using TF 2.0 you can execute the code below from your notebook environment, prior to your magic command:

```
%load_ext tensorboard
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2022-06-04 03:00:51

------------

That's how I solved it

After `--logdir`, this is my path directory `/content/drive/MyDrive/Dog\ Vision/logs`. It should be different for you.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-01-21 01:34:21

------------



I needed to pip uninstall both `tensorflow` and `tensorboard`

Then install `tf-nightly`

In order to get this to work

```
%load_ext tensorboard.notebook
%tensorboard --logdir {logs_base_dir}
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-04-19 18:14:01

------------

extension loading is required before. You can try -> `%load_ext tensorboard` 
.It worked for me. I am using TensorFlow 1.>


------------
    
    