
# Post \#68723861 [Link](https://stackoverflow.com/questions/68723861/)

## How to change menu item spacing in Firefox Proton

**Vote**: 0 (696/702) **Views**: 2412 (589/702) 

**Internal ID** \#0-1-143

Created at 2021-08-10 08:58:47

Tags: `css` `firefox`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

Firefox Proton increased the menu item spacing to levels that displease me. I heard this kind of problem can often be solved by modifying CSSes internal to Firefox. Which CSS should I change, and how, to reduce the spacing between menu items?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2021-08-11 07:42:39

------------

about:config toolkit.legacyUserProfileCustomizations.stylesheets true
Create folder and file C:\Users\XXX\AppData\Roaming\Mozilla\Firefox\Profiles\XXX.default-release\chrome\userChrome.css and copy the following lines at the top. Restart Firefox. If it doesn't work, delete all the lines in the file and leave only these:
```
/* Bookmark and context menu spacing */
menupopup > menuitem, menupopup > menu {padding-block: 0px !important;}
/* Options menu spacing */
:root {--arrowpanel-menuitem-padding: 0px 0px !important;}
/* Disable rounded tabs */
.tab-background{border-radius: 0px 0px !important; margin-bottom: 0px !important;}
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-08-19 13:13:30

------------

Not enough rep to comment on wyxchari answer, but the added info is worthwhile.
Added this to the bottom of my userChrome.css.  It had no effect.  Added it at the top and it worked and the other changes I had made (tabs on bottom) also worked.  Thanks much


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-09-07 17:13:29

------------

Firefox 92.0 changes the spacing of bookmarks again !!!!!!!!!!!!!!!!!!!!!!!!
To fix it you have to add something new to the userChrome.css explained in the previous messages. I put it complete:
```
/* Bookmark and context menu spacing */
menupopup > menuitem, menupopup > menu {padding-block: 0px !important; min-height: 0px !important;}
/* Options menu spacing */
:root {--arrowpanel-menuitem-padding: 0px 0px !important;}
/* Disable rounded tabs */
.tab-background{border-radius: 0px 0px !important; margin-bottom: 0px !important;}
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-09-11 02:55:23

------------

I think this is the best setting.
```
/* Bookmark and context menu spacing */
menupopup > menuitem, menupopup > menu {padding-block: 4px !important; min-height: 0px !important;}
/* Options menu spacing */
:root {--arrowpanel-menuitem-padding: 8px 8px !important;}
/* Disable rounded tabs */
.tab-background{border-radius: 8px 8px 0px 0px !important; margin-bottom: 2px !important;}
```



------------
    
    