
# Post \#64819790 [Link](https://stackoverflow.com/questions/64819790/)

## Angular Update 10.2 to 11

**Vote**: 10 (387/702) **Views**: 12317 (391/702) 

**Internal ID** \#0-0-111

Created at 2020-11-13 11:05:29

Tags: `angular` `angular10` `angular11`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm trying to update Angular in my project from version 10.2 to 11.0.
Running ng update:
```
@angular-devkit/build-angular      0.1002.0 -> 0.1100.1     ng update @angular-devkit/build-angular
  @angular/cdk                       10.2.7 -> 11.0.0         ng update @angular/cdk
  @angular/cli                       10.2.0 -> 11.0.1         ng update @angular/cli
  @angular/core                      10.2.3 -> 11.0.0         ng update @angular/core
  @angular/material                  10.2.7 -> 11.0.0         ng update @angular/material
```

Unfortunately each package I tried to update fails. I tried with --force and --allowDirty flags.
@angular-cli
```
npm ERR! Found: @angular-devkit/build-angular@0.1002.0
npm ERR! node_modules/@angular-devkit/build-angular
npm ERR!   dev @angular-devkit/build-angular@"~0.1100.1" from the root project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! dev @angular-devkit/build-angular@"~0.1100.1" from the root project
npm ERR! 
npm ERR! Conflicting peer dependency: @angular/compiler-cli@11.0.0
npm ERR! node_modules/@angular/compiler-cli
npm ERR!   peer @angular/compiler-cli@"^11.0.0 || ^11.0.0-next" from @angular-devkit/build- 
angular@0.1100.1
npm ERR!   node_modules/@angular-devkit/build-angular
npm ERR!     dev @angular-devkit/build-angular@"~0.1100.1" from the root project
```

@angular-devkit/build-angular
```
npm ERR! ERESOLVE unable to resolve dependency tree
npm ERR! 
npm ERR! Found: @angular-devkit/build-angular@0.1002.0
npm ERR! node_modules/@angular-devkit/build-angular
npm ERR!   dev @angular-devkit/build-angular@"~0.1100.1" from the root project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! dev @angular-devkit/build-angular@"~0.1100.1" from the root project
npm ERR! 
npm ERR! Conflicting peer dependency: @angular/compiler-cli@11.0.0
npm ERR! node_modules/@angular/compiler-cli
npm ERR!   peer @angular/compiler-cli@"^11.0.0 || ^11.0.0-next" from @angular-devkit/build-angular@0.1100.1
npm ERR!   node_modules/@angular-devkit/build-angular
npm ERR!     dev @angular-devkit/build-angular@"~0.1100.1" from the root project
```

@angular/material
```
Installing packages (npm)...npm ERR! code ERESOLVE
npm ERR! Cannot read property 'length' of undefined
```

@angular/cdk
```
Found: @angular-devkit/build-angular@0.1002.0
npm ERR! node_modules/@angular-devkit/build-angular
npm ERR!   dev @angular-devkit/build-angular@"~0.1100.1" from the root project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! dev @angular-devkit/build-angular@"~0.1100.1" from the root project
npm ERR! 
npm ERR! Conflicting peer dependency: @angular/compiler-cli@11.0.0
npm ERR! node_modules/@angular/compiler-cli
npm ERR!   peer @angular/compiler-cli@"^11.0.0 || ^11.0.0-next" from @angular-devkit/build- 
angular@0.1100.1
npm ERR!   node_modules/@angular-devkit/build-angular
npm ERR!     dev @angular-devkit/build-angular@"~0.1100.1" from the root project
```

UPDATE
After installing with specific order I managed to update, but now the NPM won't install giving this message:
```
Found: @angular-devkit/build-angular@0.1002.0
npm ERR! node_modules/@angular-devkit/build-angular
npm ERR!   dev @angular-devkit/build-angular@"~0.1100.1" from the root 
project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! dev @angular-devkit/build-angular@"~0.1100.1" from the root 
project
npm ERR! 
npm ERR! Conflicting peer dependency: @angular/compiler-cli@11.0.0
npm ERR! node_modules/@angular/compiler-cli
npm ERR!   peer @angular/compiler-cli@"^11.0.0 || ^11.0.0-next" from 
@angular-devkit/build-angular@0.1100.1
npm ERR!   node_modules/@angular-devkit/build-angular
npm ERR!     dev @angular-devkit/build-angular@"~0.1100.1" from the 
root project
```



----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2020-11-16 15:58:10

------------

Had the same problem. For me the problem was that the code editor was open,
On closing the editor and running `ng update @angular/core @angular/cli` angular successfully updated


------------
    
    
## Answer \#1

 Vote: 7

Created at 2020-11-14 18:02:46

------------

I had the exact same problem, tried different orders for upgrades, removed node_modules folder several times, and had still no luck.
For me, the problem was that I was using Node v15. I uninstalled it and re-installed Node v14 -> works


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-11-27 13:26:20

------------

I had the same problem, just delete node_modules, package-lock.json, and run  `npm install`, and everything is working now!


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-05-22 13:00:41

------------

In my case
- - - 
I assume issue is after node upgrade to v14


------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-11-14 10:21:08

------------

i had the same problem, i ended up using yarn package manager, and it resolved the issue.


------------
    
    