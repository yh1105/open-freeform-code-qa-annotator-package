
# Post \#62241170 [Link](https://stackoverflow.com/questions/62241170/)

## Live server installed but not working in VS code

**Vote**: 6 (473/702) **Views**: 50541 (162/702) 

**Internal ID** \#0-2-167

Created at 2020-06-07 05:19:14

Tags: `javascript` `html` `css` `visual-studio-code`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I have installed the live server extension but the browser does not update after I save my HTML or other files. What might be causing a problem?


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-10-29 08:01:10

------------

Re-install the extension and restart your text editor to fix the issue.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2020-12-16 13:55:47

------------

I had this problem and I solved it by changing the port number in the `settings.json` file to an opened one.
To get to the `settings.json` file you just click ++, then type "settings", click the "Preferences: Open Settings (JSON)", then paste this inside the `{ }`:
```
"liveServer.settings.port": 0
```

This will randomly pick an opened port each time. This is how my JSON file looks like:
```
{
    "liveServer.settings.port": 0
}
```

If there are multiple lines in the JSON file, make sure each line is separated by a comma(`,`), like this:
```
{
    "liveServer.settings.CustomBrowser": "microsoft-edge",
    "liveServer.settings.port": 0
}
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-07-09 18:21:27

------------

I have gone through many solutions and picked few combinations and all of a sudden it worked. So here are the steps:
Go to system environment variables > system variables > click on path > edit > add new path line : C:\Windows\System32 > apply it and close.
Now open VS Code and (re-)install Live Server.
Go to extension settings of Live Server.
Click on "Edit in settings.json" and add below statements:
"liveServer.settings.root": "/",
"liveServer.settings.port": 63404, (this port number varies)
Now go back to extension settings again and scroll down to set Live Server Custom Browser. Choose Null from the dropdown.
Note: These points all together solved my issue and may not work or work for others.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-04-30 00:11:12

------------

If live server is not runnning and rendering html page on port 5500,for example [http://127.0.0.1:5500/index.html](http://127.0.0.1:5500/index.html) not working.
Then this may be due to connection issue on your system.
I had same problem when i was compiling below html page:
```
<!DOCTYPE html>
<html>
<body>
    <h1>Hello world</h1></body>
</html>
```

Solved it by changing live server settings to my local IP and it fixed issue.
My local ip: [http://192.168.2.16:5500/index.html](http://192.168.2.16:5500/index.html) is rendering changes on fly and working.
For steps, you can refer:
[https://www.youtube.com/watch?v=_B3RRVhgCOw](https://www.youtube.com/watch?v=_B3RRVhgCOw)
This works for anyone for whom live server is not running by default with port 5500.


------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-07-23 15:32:53

------------

It is because you have added insufficient paths in your Environmental variables.

1. First you can do is close the vs code
2. Go to the properties of My Pc
3. Click on Advanced Settings
4. click on Environmental Variables
5. from there you can add path clicking the path variables in the table shown there 6 . Add a new line to the path and copy paste the path of your system 32


C:\Windows\System32
hope this solution works in your case


------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-01-31 12:55:10

------------

Live server started but still isn't opening the page in your browser automatically
```
open your preferred browser and type http://127.0.0.1:5500/<if_directory_name>/<else_direct_your_file_name>

example:

http://127.0.0.1:5500/index.html
-or-
http://127.0.0.1:5500/your_folder_name/index.html
```

 some time port number 5500 will be changed to 5501


------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-02-22 07:46:36

------------

I had a space in the file name, where the `index.html` was to be found.
Like: `3. Typography`.
Just got rid of the space: `3.Typography` because (of course) we can not have empty spaces in a url.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2021-03-04 15:28:15

------------

its easy to solve. just open setting by ( + )and pick  first field is a  Its value is by default  you must change it to 


------------
    
    
## Answer \#8

 Vote: 0

Created at 2021-09-23 14:26:23

------------

Please use the proper HTML syntax which is 
```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hello, World</title>
</head>

<body>
  My first Web dev code
</body>

</html>
```


Being a newbie to web development, I was only using the `head tag`. The web page was working in the browser but the live server couldn't process it. You can get the above snippet in vscode by typing `html:5` and then hitting tab.
Also, make sure that the `autosave` feature in vscode is enabled, which you can do by going to vscode settings `shortcut : Ctrl + ,` and then type autosave. Change `Files: Auto Save` to  and if you want to view the changes in your code instatenously, edit `Files: Auto Save Delay` to  .
Now sit back and watch the magic happen.


------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-02-16 13:54:12

------------

I was using this in a folder and folder's name has some irregular letters like capital i "İ". When I chance the folder's name to suits english rules it works.


------------
    
    
## Answer \#10

 Vote: 0

Created at 2022-03-08 15:59:35

------------

This is happening because you have changed some default settings. You should reset them all to run the live server. Unless you should define what did you change to the live server.
If the live server still not working,

1. Make sure you have embedded your folder to VS code. If you didn't just press Ctrl + B and Click on the Add New Folder Icon. Then choose your project's folder. Then right-click on your project.html and it will open.
2. If didn't work, type in the Address bar in your browser like 127.0.0.1:5500. Localhost server should open now.
3. Still, you had the problem, they try to close and reopen VS code several times.
4. If still, you got the problem, try to install your default web browser in the default path in your C drive. (C://Program files.....)
5. If you still have the problem it might be a problem with the live server. You should uninstall it and install another version outed lately.




------------
    
    
## Answer \#11

 Vote: 0

Created at 2022-04-09 19:22:41

------------

In my case reinstalling did not help.
After investigation of settings (JSON) I found that regardless removal of the extension I still have option from legacy version which tells Live Server to communicate to legacy Chrome Extension. So, you can search for
```
"liveServer.settings.useWebExt": true,
```

If you see such string, you can either comment it out or just delete the line.
This worked for me.


------------
    
    
## Answer \#12

 Vote: 0

Created at 2022-04-22 12:52:55

------------

Well, l am not quite sure why this worked for me. l changed my default browser from  to  in VSCode under . Restart VS Code and try again.


------------
    
    
## Answer \#13

 Vote: 0

Created at 2022-07-27 19:39:54

------------

for me my extension was installed and I could see go live button, but when I clicked on that did not work. the solution was in my variable path. when I change the path to the correct one with set "PATH=%PATH%;C:\Users\mesimohi\AppData\Local\Programs\Python\Python310\Scripts"
command it worked!


------------
    
    
## Answer \#14

 Vote: -1

Created at 2021-05-07 06:19:55

------------

I have same problem then i solve it by deleting  all settings of live server from settings.json file and reinstall live server extension and now running it correctly.
[vs code](https://code.visualstudio.com/)


------------
    
    
## Answer \#15

 Vote: -1

Created at 2021-07-30 08:57:08

------------

Turn on the autosave feature on. File-> AutoSave


------------
    
    
## Answer \#16

 Vote: -1

Created at 2021-11-10 15:15:53

------------

I came across this issue a few times in VSCode and for me it was because I was calling live-server from PS prompt.  I switched to "command prompt" and it worked just fine.
[Switch from PowerShell to Command Prompt](https://i.stack.imgur.com/4IKrc.png)


------------
    
    
## Answer \#17

 Vote: -1

Created at 2021-12-17 11:10:19

------------


## Best way to solve 5500 port number error


Check the port number below in the Go Live section if it is (5500 port number)
then go to browser window and type localhost:5500 then select your project folder then you can see the automatic update.


------------
    
    
## Answer \#18

 Vote: -1

Created at 2022-08-01 17:32:21

------------

I had the same problem, sometimes it is because of HTML structure, Live server doesn't really work if the HTML structure is not proper. The file should have   and  tags.


------------
    
    
## Answer \#19

 Vote: -1

Created at 2022-08-04 08:57:04

------------


1. Open the Extensions list (ctrl+shift+X)
2. Go to Live Server Five Server (I'm using this extension)
3. Open the context menu (right mouse button) or click on the settings icon
4. Select ENABLE (WORKSPACE)


[](https://i.stack.imgur.com/e7mjx.png)


------------
    
    
## Answer \#20

 Vote: -1

Created at 2022-10-04 18:13:16

------------




------------
    
    
## Answer \#21

 Vote: -1

Created at 2023-01-27 05:40:41

------------




------------
    
    
## Answer \#22

 Vote: -4

Created at 2022-01-11 09:32:08

------------

Open 'explorer' window, press open to folder button and browse your project folder.
it work for me


------------
    
    
## Answer \#23

 Vote: -6

Created at 2021-07-02 05:36:29

------------

Or check if the name of your file contains spaces, like I named my file "Countdown Project.html" and it didn't work but as soon as I realised that the  space between "Countdown" and "Project" is the problem the issue resolved. Name all your projects without spaces.


------------
    
    