
# Post \#60410285 [Link](https://stackoverflow.com/questions/60410285/)

## expo version command shows running scripts disabled on this machine

**Vote**: 14 (336/702) **Views**: 34519 (209/702) 

**Internal ID** \#0-0-107

Created at 2020-02-26 09:09:12

Tags: `windows` `powershell` `react-native` `terminal` `windows-10`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to develop a react native project in my Windows 10 machine. I installed node js then expo cli via visual studio code terminal. Then I tried the command `expo --version`, the terminal shows error::

```
expo : File C:\Users\saka\AppData\Roaming\npm\expo.ps1 cannot be loaded because running scripts is disabled on this system. For more 
information, see about_Execution_Policies at https:/go.microsoft.com/fwlink/?LinkID=135170.
At line:1 char:1
+ expo --version
+ ~~~~
    + CategoryInfo          : SecurityError: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```


the given link shows many things, but what shall I do to solve my problem? 
Thank You!!!


----------
        
## Answer \#0

**Accepted** Vote: 48

Created at 2020-03-16 02:44:30

------------

Just try to open Windows PowerShell run as administrator and then run this command line:
```
Set-ExecutionPolicy RemoteSigned
```



------------
    
    
## Answer \#1

 Vote: 20

Created at 2020-12-01 18:35:41

------------

Step1 : start power shell in administrator mode.
Step2 : Type the following command "set-ExecutionPolicy RemoteSigned"
Step3: Press Y for your Confirmation.


------------
    
    
## Answer \#2

 Vote: 10

Created at 2021-07-16 14:52:24

------------

Paste this code in your terminal
```
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted
```

Press Enter
Enjoy :)


------------
    
    
## Answer \#3

 Vote: 2

Created at 2020-03-30 11:43:38

------------

Trying to run expo start command via the integrated terminal of visual studio IDE which is by . 

In such case you open the CMD and  and then type `expo start` in the project directory. This will work.

For more information please [visit this link](https://learn.microsoft.com/en-us/visualstudio/ide/user-permissions-and-visual-studio?view=vs-2019)


------------
    
    
## Answer \#4

 Vote: 2

Created at 2020-05-27 05:30:05

------------

Step 1: You need to open your power shell or command line with ‘Run as Administrator’ by right-clicking on its icon.

Step 2: You need to type in the following command:

> Set-ExecutionPolicy RemoteSigned

[refer](https://i.stack.imgur.com/kLIEX.png)

Step 3: You will see the following message and permission request. Type ‘a’ and enter

[refer](https://i.stack.imgur.com/Egrgl.png)

Solution 2:

Try running the following command in PowerShell after running as administrator.

> Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy unrestricted

Now go and tried installing and then running ‘Expo’


------------
    
    
## Answer \#5

 Vote: 2

Created at 2020-11-09 05:59:10

------------

Solution 1:
Trying to run expo start command via the integrated terminal of visual studio IDE which is by default not authorized.
In such a case, you open the CMD and run it as an  and then type  in the project directory. This will work.
Solution 2:
Try running the following command in PowerShell after running as .

type  Yes to All
Now go and tried installing and then running ‘Expo’


------------
    
    
## Answer \#6

 Vote: 2

Created at 2021-05-06 15:47:59

------------

In case You are using Command Prompt follow this:

1. Open CMD and Run it as Administrator


2:  powershell.exe Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy unrestricted

1. run: expo & you have all set




------------
    
    
## Answer \#7

 Vote: 1

Created at 2020-07-29 16:09:54

------------

How to set PowerShell script execution policy within Visual Studio Code:
Open Settings, and click/tap on the Update & security icon. 2. Click/tap on For developers on the left side, check the Change execution policy to allow local PowerShell scripts to run without signing. Require signing for remote scripts. box under PowerShell on the right side, and click/tap on the Apply button.
do this thing your problem must be solve if u r windows 10 user


------------
    
    
## Answer \#8

 Vote: 1

Created at 2022-05-12 08:38:42

------------

. Open Settings, and click/tap on the Update & security icon. 2. Click/tap on For developers on the left side, check the Change execution policy to allow local PowerShell scripts to run without signing. Require signing for remote scripts. box under PowerShell on the right side, and click/tap on the Apply button.
do this thing your problem must be solve if u r windows 10 user
100% Working


------------
    
    
## Answer \#9

 Vote: 1

Created at 2022-11-20 14:55:08

------------

Just open cmd on Windows (not Visual Code Terminal), works fine for me. If you are in cmd, run `expo --version` and see if you still get any errors.
It's not a secure way to deactivate this setting, based on what Microsoft says: [https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.3](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.3)


------------
    
    
## Answer \#10

 Vote: 0

Created at 2021-02-26 13:35:59

------------

I faced the same issue while using Expo commands on Windows PowerShell but ran the same command on Windows cmd without error.


------------
    
    
## Answer \#11

 Vote: 0

Created at 2022-01-14 10:12:07

------------

This occurred while I was working in the VSCode terminal and the default option was PowerShell, I changed it to the cmd and it appears to be fixed. In my opinion, this is a much better fix than allowing any script to be executed by PowerShell.


------------
    
    
## Answer \#12

 Vote: 0

Created at 2022-01-27 16:27:52

------------

Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted


------------
    
    