
# Post \#56866981 [Link](https://stackoverflow.com/questions/56866981/)

## File is locked by Visual Studio 2019

**Vote**: 10 (387/702) **Views**: 25250 (261/702) 

**Internal ID** \#2-6-400

Created at 2019-07-03 09:27:25

Tags: `c#` `visual-studio` `visual-studio-2019` `locked-files`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I have a weird problem in Visual Studio:
if an  during the debugging process I'm not able to build my
C# application:
```
Unable to copy file "C:\Projects\A\bin\A.dll" to "..\..\bin\A.dll". 
  The process cannot access the file '..\..\bin\A.dll' because it is being used 
  by another process.

Could not copy "C:\Projects\A\bin\A.dll" to "..\..\bin\A.dll". 
  Exceeded retry count of 10. Failed. 
  The file is locked by: "Microsoft Visual Studio 2019 (14904)"
```

Is there a permanent solution to solve this strange error? (my only workaround is currently to restart Visual Studio)
What I tried and not worked for me:
- - - - - 
It's also not practical to insert Pre-build event scripts in all of my projects! Link: [https://blog.ndepend.com/visual-studio-script-saves-time-pain/](https://blog.ndepend.com/visual-studio-script-saves-time-pain/)
Does anyone have a solution to this problem?


----------
        
## Answer \#0

**Accepted** Vote: 6

Created at 2019-07-22 07:10:03

------------

It's a little bit weird but the problem was caused by the Debug option `Use Managed Compatibility Mode`! So just untick Debug -> Options -> `Use Managed Compatibility Mode` and no more files locked errors by Visual Studio!
 The downside of disabling this option is you will get annoying long time consuming evaluation errors by VS! ([More information](https://stackoverflow.com/questions/52187355/visual-studio-2017-debug-error-to-prevent-an-unsafe-abort-when-evaluating-the-f))


------------
    
    
## Answer \#1

 Vote: 20

Created at 2019-10-14 15:05:05

------------

What worked for me - Close the Solution, Delete the bin and obj folder, re-open and build.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-07-24 07:50:11

------------

Deleting the obj and bin folders were impossible for me, so i had to install Microsoft's Process Explorer to find the application's dll process still running. Killing this process worked for me.


------------
    
    
## Answer \#3

 Vote: 0

Created at 2020-11-07 08:16:04

------------

it is a little stupid but:
In windows task manager in Process tab, just find Microsoft visual studio <2019>, click "^" to collapse it's child's tree and find "Microsoft Visual Studio XAML Designer"
select "Microsoft Visual Studio XAML Designer"  and simply end process it before every debug.


------------
    
    
## Answer \#4

 Vote: 0

Created at 2021-01-04 09:42:34

------------

Deleting was impossible for me but it turned out that VS2019 kept a debug version of the software running that didn't close automatically when I stopped debugging. In Task manager -> close the running app and I could start the build/debug again.


------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-04-22 14:26:52

------------

I have encountered this in a WinForms project in VS2019, where none of the other suggested solutions has worked,  and where exiting VS and attempting to delete the affected files was refused by the O/S still claiming the files were open.
The fix that has worked here in this case was to go to Task Manager, and to find and terminate a copy of VBCSCompiler that is being left running even after VS has terminated.   It shows up under "Background Processes".
After that, starting VS again worked fine.


------------
    
    
## Answer \#6

 Vote: 0

Created at 2022-05-24 18:33:53

------------

Deleting worked for me as well but only after I force exited the DTSDebugHost from taskmgr for any others that couldn't get it to work.


------------
    
    
## Answer \#7

 Vote: -1

Created at 2021-10-19 22:38:07

------------

Run Clean before Building or use Rebuild option. VS will build it fine. Bit slow , but it works everytime. No VS restart required.


------------
    
    
## Answer \#8

 Vote: -2

Created at 2021-06-07 18:34:53

------------

Simple fix in my case. I already had my application running when I tried to run it again and got this error. Closing it solved my problem.


------------
    
    