
# Post \#66698247 [Link](https://stackoverflow.com/questions/66698247/)

## VSCode: The Python path in your debug configuration is invalid

**Vote**: 8 (428/702) **Views**: 18293 (316/702) 

**Internal ID** \#1-3-221

Created at 2021-03-18 19:55:59

Tags: `python` `python-3.x`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

Very new to Python and VSCode (and stackoverflow). I've been using both for about 3 months now just fine, up until recently that is.
When trying to run any basic Python program in the debugger, the popup `The Python path in your debug configuration is invalid. Source: Python(Extension)` appears and the debugger won't run. I go to my `launch.json` file and sure enough, I have the path to where Python is set up.
{
```
"version": "0.2.0",
"configurations": [
    {
        "name": "Python: Current File",
        "type": "python",
        "request": "launch",
        "program": "${file}",
        "console": "integratedTerminal",
        "python": "${command:python.interpreterPath}"
    }
]
```

}
Messing with `settings.json` doesn't help anything either because I do have the path to Python set up, but the debugger still won't run. I am at a loss what to do here. I have never gone into my .json files in the past before, nor have I ever had to configure my Python path after installing VSCode for the first time.


----------
        
## Answer \#0

**Accepted** Vote: 5

Created at 2021-03-18 21:58:27

------------

Turns out I had to downgrade the version of my Python extension from Version 2021.3 to 2021.2, now VSCode can finally find the Python path.


------------
    
    
## Answer \#1

 Vote: 16

Created at 2021-03-31 10:06:55

------------

I was facing the same problem. Here's how I fixed it without having to uninstall:
Look at this image for reference:
[](https://i.stack.imgur.com/w9i7q.png)
Click on the "Python 3.8.3 32-bit" down there in the corner (the version maybe different for you) and select your debugger or specify the location if it's not already there.


------------
    
    
## Answer \#2

 Vote: 8

Created at 2021-04-07 07:42:29

------------

Vscode is not able to find the python path or its not yet set.
Open command palette(ctrl+shift+P) and type python and look for "Python:Select Interpreter".
You will be asked to enter the path of python where it is installed.
Incase you are using virtual environment and in most of the case its true. Look for the path .venv/Script/python
else select the python path where you have installed in your local machine.


------------
    
    
## Answer \#3

 Vote: 5

Created at 2021-07-31 17:32:32

------------


### Tl;Dr


Restart Vscode

### Findings to get there


- `extensions``ctrl+shift+x`- `Reload required``Python Extension`- - 


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-03-18 20:45:19

------------

You could try something like this but directing it to wherever your python program is install on your system
```
{
    "python.pythonPath": "C:\\Python36\\python.exe"
}
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-03-07 08:06:48

------------

My Case:
I couldn't find the underlying reason, but could find a workaround as follows.
- - 
This solves the problem and the code runs normally.
In short, my workaround is to use VSCode as only an editor, and run codes via terminals (command prompt, power shell etc.).


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-04-11 10:19:44

------------

add `"cwd": "${fileDirname}"`
like so
```
"configurations": [
    {
      "name": "Python: Current File",
      "type": "python",
      "request": "launch",
      "program": "${file}",
      "console": "integratedTerminal",
      "justMyCode": true,
      "cwd": "${fileDirname}"
    },
...
```



------------
    
    
## Answer \#7

 Vote: -1

Created at 2021-05-01 12:51:20

------------

I had a similar problem in VScode today. I was getting the following error message every time I tried to run a script.
> The Python path in your debug configuration is invalid.
The problem resolved itself when I opened a folder in the 'Explorer' tab on the left hand side of the screen. Not sure why this worked or if it was actually the problem ... maybe it will help someone.


------------
    
    