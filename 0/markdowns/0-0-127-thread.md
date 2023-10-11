
# Post \#72768067 [Link](https://stackoverflow.com/questions/72768067/)

## When I open the terminal shows "The specified command("completion") is invalid. For a list of available options, run "ng help"

**Vote**: 26 (219/702) **Views**: 6197 (500/702) 

**Internal ID** \#0-0-127

Created at 2022-06-27 06:58:53

Tags: `angular` `terminal` `iterm2`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

When I open the terminal did't do anything. It will shows errors as below.
[](https://i.stack.imgur.com/lx1jC.png)


----------
        
## Answer \#0

**Accepted** Vote: 46

Created at 2022-06-28 10:39:26

------------

I had the same error after updating angular every time after opening the terminal.
Depending on which terminal you use either open configuration file which will be saved in ~/.bashrc, or for zshell ~/.zshrc, and so on for others,
Edit the corresponding `~/.bashrc` or `~/.zshrc` file and delete or comment this line:
```
source <(ng completion script)
```

Sadly you will lose the angular autocompletion, but it looks like there is some bug causing this in their impl}ementation


------------
    
    
## Answer \#1

 Vote: 17

Created at 2022-08-15 10:50:03

------------

According to [angular.io](https://angular.io/cli/completion), `ng completion` will help you in command line mode. So, if you face the problem, you can try to fix it rather than simply remove it. This command works in Angular 14.
First you should try to upgrade your `@angular/cli` by below commands:
```
npm uninstall @angular/cli
npm i --location=global @angular/cli
```

After this, you should try to open a new terminal, see if the problem still exists.
If yes, then probably you used a wrong `ng`, by running command `which ng`, it will tell you the "global" `ng` location. Almost 100 percent it is not the one you have just installed.
So you can just delete this `ng` and it's related `node_modules/@angular`, then re-install angular cli.
It should work now.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2022-11-20 00:48:04

------------

Make sure you upgrade your angular/cli to the latest version as suggested by Bryan. I was getting the same error but was able to resolve once I upgraded my angular cli version to latest


------------
    
    