
# Post \#53680346 [Link](https://stackoverflow.com/questions/53680346/)

## The generate command requires to be run in an Angular project, but a project definition could not be found

**Vote**: 13 (343/702) **Views**: 89611 (92/702) 

**Internal ID** \#0-0-84

Created at 2018-12-08 07:04:01

Tags: `angular`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

at the time I'm going to create component using ng g component command I facing with this error what I need to do to solve this.

"The generate command requires to be run in an Angular project, but a project definition could not be found."


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2018-12-08 08:03:11

------------

Maybe your problem is from Angular/CLI version, Check Angular/CLI installed on your pc by the below code in CMD.

```
ng version
```


[](https://i.stack.imgur.com/ByrPy.jpg)

The latest version is 7.1.2. If your version is lower than this version,
please use:

```
npm install -g @angular/cli
```


to get the latest version and update the last packages which you used in your project by [npm-check-updates](https://github.com/tjunnone/npm-check-updates).

Otherwise, probably your problem is from file angular.json config.


------------
    
    
## Answer \#1

 Vote: 7

Created at 2019-11-16 09:04:39

------------


1. Check you are in correct project or not.
2. Check angular.json file in your project first,if it is missing,add file.
3. Update angular CLI.




------------
    
    
## Answer \#2

 Vote: 6

Created at 2019-02-18 10:42:58

------------

If you trying to add new component to an existing project, you should install packages on your system first.

run this code on your console at your root project folder:

```
npm install
```



------------
    
    
## Answer \#3

 Vote: 5

Created at 2018-12-08 08:35:51

------------

Your project folder must either contain `angular.json` or `angular-cli.json`.

The above mentioned file contains your project definition and its structure. So, that's why without these files and without `angular-cli` installed. You won't be able to run,
`ng g component` command successfully.


------------
    
    
## Answer \#4

 Vote: 3

Created at 2021-01-08 21:58:02

------------

You can also get this behavior if you are confusing ng new  with ng g application .


------------
    
    
## Answer \#5

 Vote: 2

Created at 2020-12-23 16:39:54

------------

first
`cd "project name"`
then
`ng serve` or `npm start`


------------
    
    
## Answer \#6

 Vote: 1

Created at 2022-02-11 09:21:28

------------

After ng new project make sure you entered project directory in terminal.


------------
    
    
## Answer \#7

 Vote: 0

Created at 2019-02-12 18:08:45

------------

first you check whether you are in workspace location or in your angular-application location.
If you are in workspace location then type in command prompt

> \workspace>cd angular-application

now you are in following location

here you type ng serve then your application compile and build

> \workspace\angular-application>ng serve

you getting below information 

> C:\Users\venkat\Documents\angular\myfirstangular>ng serve --port 4300

Angular Live Development Server is listening on localhost:4300, open your browser on [http://localhost:4300/](http://localhost:4300/) 
 95% emitting CopyPlugini ｢wdm｣: wait until bundle finished: /


## Date: 2019-02-12T18:15:21.157Z



chunk {styles} styles.js, styles.js.map (styles) 16.3 kB [initial] [rendered]
chunk {vendor} vendor.js, vendor.js.map (vendor) 3.75 MB [initial] [rendered]
i ｢wdm｣: Compiled successfully.


------------
    
    
## Answer \#8

 Vote: 0

Created at 2019-11-19 13:11:10

------------

you need to add fire npm install command in your root directory of project then npm start.
it works for me


------------
    
    
## Answer \#9

 Vote: 0

Created at 2019-12-23 17:52:50

------------

Navigate to project folder and execute command again


------------
    
    
## Answer \#10

 Vote: 0

Created at 2021-06-15 08:00:36

------------

Run ng serve command in client folder


------------
    
    