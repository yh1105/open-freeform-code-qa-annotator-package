
# Post \#53004311 [Link](https://stackoverflow.com/questions/53004311/)

## How to add conda environment to jupyter lab

**Vote**: 183 (23/702) **Views**: 131075 (63/702) 

**Internal ID** \#1-3-273

Created at 2018-10-26 08:13:26

Tags: `python` `anaconda` `jupyter-notebook` `jupyter-lab`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I'm using Jupyter Lab and I'm having trouble to add `conda` environment. The idea is to launch Jupyter Lab from my base environment, and then to be able to choose my other conda envs as kernels.

I installed the package `nb_conda_kernels` which is supposed to do just that, but it's not working as I want. Indeed, let's assume I create a new Conda Environment, then I launch jupyter lab from base, I can't see the new environment as an available kernel.

I have found a "fix", which works everytime but is not convenient at all. If I install Jupyter Notebook in my new environment, then launch a jupyter notebook from this new environment, close it, go back to base environment, and then launch Jupyter Lab from base environment, my new environment is available as a kernel in Jupyter Lab.

If you know how to make it work without this "fix", I would be very grateful.


----------
        
## Answer \#0

**Accepted** Vote: 52

Created at 2019-10-02 09:23:20

------------

A solution using `nb_conda_kernels`. First, install it in your base environment :

```
(base)$ conda install -c conda-forge nb_conda_kernels
```


Then in order to get a kernel for the conda_env `cenv` :

```
$ conda activate cenv
(cenv)$ conda install ipykernel
(cenv)$ conda deactivate
```


You will get a new kernel named `Python [conda env:cenv]` in your next run of `jupyter lab` / `jupyter notebook`

Note :
If you have installed `nb_conda_kernels`, and want to create a new conda environment and have it accessible right away then

```
conda create -n new_env_name ipykernel
```


will do the job.


------------
    
    
## Answer \#1

 Vote: 342

Created at 2018-11-29 19:57:11

------------

Assuming your conda-env is named `cenv`, it is as simple as :
```
$ conda activate cenv           # . ./cenv/bin/activate in case of virtualenv
(cenv)$ conda install ipykernel
(cenv)$ ipython kernel install --user --name=<any_name_for_kernel>
(cenv)$ conda deactivate
```

If you restart your jupyter notebook/lab you will be able to see the new kernel available. For newer versions of jupyter kernel will appear without restarting the instance. Just refresh by pressing F5.
PS: If you are using virtualenv etc. the above steps hold good.


------------
    
    
## Answer \#2

 Vote: 21

Created at 2020-06-10 19:59:19

------------

I tried both of the above solutions and they didn't quite work for me. Then I encountered this medium article which solved it: [https://medium.com/@jeremy.from.earth/multiple-python-kernels-for-jupyter-lab-with-conda-c67e50de3aa3](https://medium.com/@jeremy.from.earth/multiple-python-kernels-for-jupyter-lab-with-conda-c67e50de3aa3)

Essentially, after running `conda install ipykernel` inside of your `cenv` environment, it is also good to run `python -m ipykernel install --user --name cenv` within the `cenv` environment - that way, we make sure that the version of python that is used within the jupyter environment is the one in `cenv`. Cheers!


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-04-07 14:23:33

------------

The following worked for me
```
pip install nb_conda
```

[https://github.com/Anaconda-Platform/nb_conda](https://github.com/Anaconda-Platform/nb_conda)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-09-03 10:04:07

------------

I couldn't get conda environment to show up in jupyter lab as well and for me worked only this:
(assuming as above 'cenv' as environment name)

1. conda activate cenv
2. conda install ipykernel
3. python -m ipykernel install --user --name=cenv




------------
    
    
## Answer \#5

 Vote: -1

Created at 2023-02-15 13:10:55

------------

useful cheat sheet from conda.
[CONDA CHEAT SHEET](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)


------------
    
    