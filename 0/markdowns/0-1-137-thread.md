
# Post \#68909199 [Link](https://stackoverflow.com/questions/68909199/)

## Invalid CSS value error while Customizing Bootstrap 5 colors with sass 3

**Vote**: 12 (356/702) **Views**: 5838 (504/702) 

**Internal ID** \#0-1-137

Created at 2021-08-24 14:34:46

Tags: `css` `sass` `bootstrap-5`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I want to change bootstrap's default theme-colors with SASS , the problem is when I change a color and compile , it gives me invalid CSS value error.
I've read the docs and saw some tutorials on YouTube but I can't see where is the problem
I'm using bootstrap 5.1.0 , sass 3
this is my scss file:
```
@import "../../node_modules/bootstrap/scss/variables";

$theme-colors: (
"primary": //some color here,
);

@import "../../node_modules/bootstrap/scss/bootstrap";
```

and this is the error I get in terminal
```
PS F:\Coding\projects\sepehr\client\src\styles> sass style.scss custom.css
Error: ("primary": #0d6efd, "secondary": #6c757d, "success": #198754, "info": #0dcaf0, 
"warning": #ffc107, "danger": #dc3545, "light": #f8f9fa, "dark": #212529) isn't a valid 
CSS value.
   ╷
94 │ $theme-colors-rgb: map-loop($theme-colors, to-rgb, "$value") !default;
   │                             ^^^^^^^^^^^^^
   ╵
```



----------
        
## Answer \#0

**Accepted** Vote: 17

Created at 2021-08-24 14:36:49

------------

You need to import functions and mixins too...
```
@import "../../node_modules/bootstrap/scss/functions";
@import "../../node_modules/bootstrap/scss/variables";
@import "../../node_modules/bootstrap/scss/mixins";
```

Also see: [Bootstrap 5 - Custom theme-colors not updating classes](https://stackoverflow.com/questions/68755179/bootstrap-5-custom-theme-colors-not-updating-classes/68762902#68762902)


------------
    
    
## Answer \#1

 Vote: 4

Created at 2021-09-16 20:18:53

------------

This is weird, as this works with @import, but as they plan to remove @import, the docs suggest using @use. This code does not work using @use, it is still missing dependencies when using @use.


------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-07-30 22:24:08

------------

Finally I found the error:
It happens, when you mix different Versions of Bootstrap, example 5.02 (or older) and 5.2. or 5.1.0
Beware that your scss --load-path does not cover older bootstrap-versions or fragments from it!
It causes functions.scss to be loaded from the older versions. These do not yet contain a map-loop function. The error above must actually mean that the function map-loop was not found! This is the compiler (or the error message to it) crap.


------------
    
    