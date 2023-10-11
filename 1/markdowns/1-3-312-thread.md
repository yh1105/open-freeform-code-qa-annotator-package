
# Post \#63187161 [Link](https://stackoverflow.com/questions/63187161/)

## error while import pytorch module. (The specified module could not be found.)

**Vote**: 9 (409/702) **Views**: 22439 (280/702) 

**Internal ID** \#1-3-312

Created at 2020-07-31 06:59:44

Tags: `python` `dll` `pytorch`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I just newly install python 3.8 via anaconda installer and install pytorch using command
```
conda install pytorch torchvision cpuonly -c pytorch
```

when i try to import torch, I got this error message.
```
OSError: [WinError 126] The specified module could not be found. Error loading "C:\Users\chunc\anaconda3\lib\site-packages\torch\lib\asmjit.dll" or one of its dependencies.
```

I can see [dll files](https://i.stack.imgur.com/s92PY.png) are still in the directory.
I ran Dependency Walker and it gave me [this result](https://i.stack.imgur.com/nsnqp.png).
I am with this problem for a day.
What should i do if i want to use PyTorch module?


----------
        
## Answer \#0

**Accepted** Vote: 29

Created at 2020-08-03 21:00:16

------------

I had the same problem, you should check if you installed Microsoft Visual C++ Redistributable, because if you didn't this may lead to the DLL load failure.
Here is a link to download it: [https://aka.ms/vs/16/release/vc_redist.x64.exe](https://aka.ms/vs/16/release/vc_redist.x64.exe)


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-08-03 07:47:17

------------

Problem solved by downgrade PyTorch version to 1.5.1


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-17 04:41:47

------------

I tried so many different solutions but I couldn't get PyTorch to work on my main conda environment. So finally I created a new environment and installed PyTorch on it; everything worked on the first try.


------------
    
    