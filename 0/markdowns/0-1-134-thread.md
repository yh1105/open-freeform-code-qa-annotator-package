
# Post \#58901066 [Link](https://stackoverflow.com/questions/58901066/)

## How to quickly create <div> classes in VSCode

**Vote**: 16 (306/702) **Views**: 57334 (147/702) 

**Internal ID** \#0-1-134

Created at 2019-11-17 13:45:31

Tags: `css`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

Generally new to web design and watching some tutorials on creating some backend for a project, getting really tired of writing out the  manually, I see youtubers do .classname and then the class with the div appears, but for some reason it isn't working for me? Any help would be appreciated.

Also, would it be easier to switch to Sublime, my buddies think that it is the way to go.

Cheers.


----------
        
## Answer \#0

**Accepted** Vote: 27

Created at 2020-09-22 14:33:22

------------


1. Go to settings
2. Go to emmet under the Extensions section.
3. Click on 'Edit in settings.json'.
4. Write the following inside the 'emmet.includeLanguages' tag. Otherwise, paste the whole statement. "emmet.includeLanguages": { "javascript":"javascriptreact" }
5. Save the settings.json file.




------------
    
    
## Answer \#1

 Vote: 20

Created at 2019-11-18 02:41:19

------------

Those videos are likely using [emmet](https://code.visualstudio.com/docs/editor/emmet). VS Code includes built-in support for emmet completions in html files. For example, typing `.classname` in an html file will trigger an emmet suggestion that expands to `<div class="classname"></div>` when you accept it

If you do not see this working:

- `html`[language mode](https://code.visualstudio.com/docs/languages/overview#_changing-the-language-for-the-selected-file)- `.classname`- 


------------
    
    
## Answer \#2

 Vote: 11

Created at 2021-05-23 09:18:02

------------

I tried everything written in the answers but it wouldnt work, I had to do the following;
go to settings in the bottom left, search for 'emmet'
scroll down to and tick:
'Trigger expansions on Tab'
then it works by typing .divClassName + Tab


------------
    
    
## Answer \#3

 Vote: 4

Created at 2019-11-17 13:49:32

------------

Check out this Cheat Sheet for VSC: 
[Cheat sheet for VSC](https://docs.emmet.io/cheat-sheet/)


------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-10-09 09:11:38

------------

Ensure that VScode recognises your file as `HTML5` or `CSS` file. In my case I had emmet enabled, but while I could get emmet abbreviation in a `CSS` file, they wouldn't work in an `HTML` file. The issue was that I also had Django template extension installed and the file had Django template code as well, hence VScode considered the file as Django template file, not HTML. You can check this the status bar at the bottom of VScode. Once I changed the file from Django template to HTML by clicking on `Django Template` in the VScode status bar, emmet started working.


------------
    
    
## Answer \#5

 Vote: 1

Created at 2022-11-11 12:56:27

------------

The above answers didn't help me because VS code already came with Emmet installed, but I was missing the information on how to actually trigger it.

### For an html element


Type the element e.g. div, h1, whatever, then press  to complete it

### For a class


Type the class name  then press  to complete it.
For example type .myclass and hit tab and you'll get `<div class="myclass"></div>`
Note: if your class has spaces, use a dot in place of the space (e.g. for "my great class", you should type ".my.great.class" and hit tab)

### Source


- [here](https://www.youtube.com/watch?v=EhRPdUv1ZrA&t=1m38s)


------------
    
    
## Answer \#6

 Vote: 0

Created at 2021-12-26 18:49:49

------------

Tried mentioned thing from emmet vs code document
- - `"emmet.triggerExpansionOnTab": true`
it worked for me for reference : [Emmet in visual studio code](https://code.visualstudio.com/docs/editor/emmet)


------------
    
    