
# Post \#49679808 [Link](https://stackoverflow.com/questions/49679808/)

## Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules/node-sass/build'

**Vote**: 45 (139/702) **Views**: 129801 (64/702) 

**Internal ID** \#0-0-116

Created at 2018-04-05 19:11:22

Tags: `node.js` `angular` `npm`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I am trying to run an Angular application. I execute the [ng serve](https://angular.io/cli/serve) command, but it then shows:
> Error: Cannot find module 'node-sass'


----------
        
## Answer \#0

**Accepted** Vote: 129

Created at 2018-12-11 05:26:53

------------


You need to change the ownership of folder [node_modules](https://stackoverflow.com/questions/63294260/what-is-the-purpose-of-the-node-modules-folder), because you use `sudo npm install -g node-sass`, so its ownership is set to root.
To change the ownership to the current user as group -
```
sudo chown -R root:$(whoami) /usr/local/lib/node_modules/

sudo chmod -R 775 /usr/local/lib/node_modules/
```

And never use `sudo` for an `npm`-related query.


------------
    
    
## Answer \#1

 Vote: 49

Created at 2019-09-03 19:53:09

------------

Run this command
```
sudo npm install -g <ModuleName> --unsafe-perm=true --allow-root
```



------------
    
    
## Answer \#2

 Vote: 22

Created at 2018-04-13 09:17:03

------------

Try this

> sudo npm install -g --unsafe-perm node-sass

Or this 

> sudo npm install -g --unsafe-perm --verbose @angular/cli


------------
    
    
## Answer \#3

 Vote: 22

Created at 2018-10-01 08:54:15

------------

I was working in [Ionic](https://en.wikipedia.org/wiki/Ionic_(mobile_app_framework)) and had this issue, so I solved this by moving one folder back and running this command:
`sudo chmod -R 777 project-directory`
And after this:
`npm install node-sass --save`


------------
    
    
## Answer \#4

 Vote: 6

Created at 2021-06-15 14:18:51

------------

This is due to the user's permission. The user from which you are running the command of `npm install` is probably doesn't have permission. So you are getting errors.
For that user just give the permission for that project folder.
In [Mac](https://en.wikipedia.org/wiki/Macintosh), use the below steps and give read and write permission to the user for that project folder
- On your Mac, select a disk, folder, or file, and then choose menu  → .- If the information in  isn’t visible, click the arrow.- If the lock at the bottom right is locked, click it to unlock the  options, and then enter an administrator name and password.- Click a user or group in the  column, and then choose a privilege setting from the pop-up menu.- Read & Write: Allows a user to open the item and change it.- Read-only: This allows a user to open the item but not change its
contents.- Write only (Drop Box): Makes a folder into a drop box. Users can
copy items to the drop box, but can’t open it. Only the owner of
the dropbox can open it.- No Access: Blocks all access to the item.
This helped me to solve the problem.


------------
    
    
## Answer \#5

 Vote: 5

Created at 2021-11-25 10:56:55

------------

This worked for me -
```
sudo chown -R `whoami` ~/.npm
sudo chown -R `whoami` /usr/local/lib/node_modules
```



------------
    
    
## Answer \#6

 Vote: 1

Created at 2018-10-01 16:18:53

------------

Try this: 
If you want the latest, then just try .
If you're getting an access problem then:

1. Windows: command prompt in administrator mode, and then run the above.
2. Mac:  sudo npm install node-sass



### References


- [ERROR in Cannot find module 'node-sass'](https://stackoverflow.com/questions/48147896/error-in-cannot-find-module-node-sass)- [Windows: 'npm install node-sass' fails when module 'install.js' cannot be found. #603](https://github.com/sass/node-sass/issues/603)


------------
    
    
## Answer \#7

 Vote: 0

Created at 2018-04-05 20:23:19

------------

If you have not added Node.js  yet, try running `node init`. If you already have this, `npm install node-sass`.
If both are present and you are using [Ubuntu](https://en.wikipedia.org/wiki/Ubuntu_%28operating_system%29), try changing the permission of the folder using `chmod -R <path to node module>`.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2020-11-18 22:51:28

------------

Make sure you are not in the  directory or other places. You need to go back to root.
Use this command
```
cd ..
cd ~
```

Your current location should look like:
```
~$
```

After this, run the command to create a project.


------------
    
    
## Answer \#9

 Vote: 0

Created at 2022-06-26 21:14:09

------------

u need to use a "root" user in your shell,
with this command:
sudo su -
then you should enter your password
and then try to install the package, i think it will work with any package that you try to install globally
more info that help me in this website
[http://www.dark-hamster.com/programming/how-to-solve-error-message-eacces-permission-denied-in-nodejs-application/](http://www.dark-hamster.com/programming/how-to-solve-error-message-eacces-permission-denied-in-nodejs-application/)


------------
    
    