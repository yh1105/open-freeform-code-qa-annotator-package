
# Post \#50306703 [Link](https://stackoverflow.com/questions/50306703/)

## How to remove package using Angular CLI?

**Vote**: 95 (65/702) **Views**: 225781 (33/702) 

**Internal ID** \#0-0-52

Created at 2018-05-12 13:22:07

Tags: `angular` `angular-cli`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

Angular 6 has new CLI command `ng add <package>`.

How can I remove the package using Angular CLI?

UPDATE

[documentation](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4)

> `ng add` will use your package manager to download new dependencies and invoke an installation script (implemented as a schematic) which can update your project with configuration changes, add additional dependencies (e.g. polyfills), or scaffold package-specific initialization code

It is not the same `npm install <package>`

I was trying to use `ng rm` but it is not working yet. 


----------
        
## Answer \#0

**Accepted** Vote: 171

Created at 2018-05-12 14:06:12

------------

You can use `npm uninstall <package-name>` will remove it from your package.json file and from node_modules.

If you do `ng help` command, you will see that there is no `ng remove/delete` supported command. So, basically you cannot revert the `ng add` behavior yet. 


------------
    
    
## Answer \#1

 Vote: 8

Created at 2019-04-24 09:08:26

------------

It's an open [issue #900](https://github.com/angular/angular-cli/issues/900) on GitHub, unfortunately at this point of time it looks that in Angular CLI there's nothing like `ng remove/rm/`..., only using `npm uninstall DEPENDENCY` is the current workaround.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-05-18 07:25:38

------------

Sometimes a dependency added with `ng add` will add more than one package, typing `npm uninstall lib1 lib2` could be error prone and slow, so just remove the not needed libraries from `package.json` and run `npm i`


------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-02-25 15:12:51

------------

I don't know about CLI, I had tried, but I couldn't. I deleted using IDE Idea history.
If You use an , just open History changes.
Tap by main folder of the .
 from top to bottom 
[](https://i.stack.imgur.com/pOmPQ.png)


------------
    
    
## Answer \#4

 Vote: 0

Created at 2018-05-12 13:25:06

------------

With the cli I don't know if it's a remove command but you can remove it from package.json and stop using it in your code. If you reinstall the packages you will not have it any more


------------
    
    