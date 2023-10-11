
# Post \#47788847 [Link](https://stackoverflow.com/questions/47788847/)

## Font Awesome 5 font-family issue

**Vote**: 79 (78/702) **Views**: 199315 (37/702) 

**Internal ID** \#0-1-140

Created at 2017-12-13 08:48:09

Tags: `css` `fonts` `font-awesome-5`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I integrated Font Awesome 5 in a project with bootstrap 4. When I recall a font via CSS it does not work.
with Font Awesome 4 the code was as follows:

```
#mainNav .navbar-collapse .navbar-sidenav .nav-link-collapse:after {
  float: right;
  content: "\f107";
  font-family: "FontAwesome";
}
```


I tried to change the font name but it does not work

```
font-family: 'Font Awesome 5 Free'
```



----------
        
## Answer \#0

**Accepted** Vote: 201

Created at 2017-12-13 09:07:50

------------

Your `Unicode` is wrong `f107`

```
a::after {
  content: "\f007";
  font-family: 'Font Awesome\ 5 Free';
}
```
```
<link href="https://use.fontawesome.com/releases/v5.0.1/css/all.css" rel="stylesheet">
<a>User</a>
<i class="fas fa-shopping-basket"></i>
```



Or in other case, `font-weight: 900;` will save you. Some icons in font awesome 5 not working without `font-weight: 900;`.

```
a::after {
  content: "\f007";
  font-family: 'Font Awesome\ 5 Free';
  font-weight: 900;
}
```



------------
    
    
## Answer \#1

 Vote: 107

Created at 2018-03-30 01:59:06

------------

Strangely you must put the '' in some icons so that it shows them.

```
#mainNav .navbar-collapse .navbar-sidenav .nav-link-collapse:after {
  content: '\f107';
  font-family: 'Font Awesome\ 5 Free'; 
  font-weight: 900; /* Fix version 5.0.9 */
}
```



------------
    
    
## Answer \#2

 Vote: 26

Created at 2018-03-01 12:50:17

------------

The problem is in the `font-weight`.
For `Font Awesome 5` you have to use `{font-weight:900}`


------------
    
    
## Answer \#3

 Vote: 15

Created at 2018-02-15 07:20:21

------------

Using the fontawesome-all.css file: Changing the "Brands" font-family from "Font Awesome 5 Free" to "Font Awesome 5 Brands" fixed the issues I was having. 

I can't take all of the credit - I fixed my own local issue right before looking at the CDN version: [https://use.fontawesome.com/releases/v5.0.6/css/all.css](https://use.fontawesome.com/releases/v5.0.6/css/all.css)

They've got the issue sorted out on the CDN as well.

```
@font-face {
    font-family: 'Font Awesome 5 Brands';
    font-style: normal;
    font-weight: normal;
    src: url("../webfonts/fa-brands-400.eot");
    src: url("../webfonts/fa-brands-400.eot?#iefix") format("embedded-opentype"), url("../webfonts/fa-brands-400.woff2") format("woff2"), url("../webfonts/fa-brands-400.woff") format("woff"), url("../webfonts/fa-brands-400.ttf") format("truetype"), url("../webfonts/fa-brands-400.svg#fontawesome") format("svg"); }

    .fab {
    font-family: 'Font Awesome 5 Brands'; }
    @font-face {
    font-family: 'Font Awesome 5 Brands';
    font-style: normal;
    font-weight: 400;
    src: url("../webfonts/fa-regular-400.eot");
  src: url("../webfonts/fa-regular-400.eot?#iefix") format("embedded-opentype"), url("../webfonts/fa-regular-400.woff2") format("woff2"), url("../webfonts/fa-regular-400.woff") format("woff"), url("../webfonts/fa-regular-400.ttf") format("truetype"), url("../webfonts/fa-regular-400.svg#fontawesome") format("svg"); }
```




------------
    
    
## Answer \#4

 Vote: 7

Created at 2018-08-22 21:46:14

------------

Requiring 900 weight is not a weirdness but a intentional restriction added by FontAwesome (since they share the same unicode but just different font-weight) so that you are not hacking your way into using the 'solid' and 'light' icons, most of which are available only in the paid 'Pro' version.


------------
    
    
## Answer \#5

 Vote: 6

Created at 2017-12-13 10:47:52

------------

Since FontAwesome 5, you have to enable a new "searchPseudoElements" option to use FontAwesome icons this way: 

```
<script>
  window.FontAwesomeConfig = {
    searchPseudoElements: true
  }
</script>
```


See also this question: [Font awesome 5 on pseudo elements](https://stackoverflow.com/questions/47712987/font-awesome-5-on-pseudo-elements) and the new Font Awesome API: [https://fontawesome.com/how-to-use/font-awesome-api](https://fontawesome.com/how-to-use/font-awesome-api)

Additionaly, change font-family in your CSS code to 

```
font-family: "Font Awesome 5 Regular";
```



------------
    
    
## Answer \#6

 Vote: 4

Created at 2018-02-08 22:57:28

------------

I didn't want to use the 'all' version, so searched the '`fontawesome-all.min.css`' file (previously included in the header) for 'family' tag and found at the end a declaration `@font-face{font-family:**Font Awesome\ 5 Free**;font-style:normal;`

So, in the stylesheet for an element where I wanted to use the `content: "\f0c8";` code, I've added (or changed to) `font-family: Font Awesome\ 5 Free;` and it worked.

```
.frb input[type="checkbox"] ~ label:before {
    font-family: Font Awesome\ 5 Free;
    content: "\f0c8";
    font-weight: 900;
    position: absolute;
}
```



------------
    
    
## Answer \#7

 Vote: 4

Created at 2019-09-26 07:15:49

------------

I had tried all above the solutions for  but it wasn't working for me. :( 

 

Just use `font-family: "Font Awesome 5 Pro";` in your CSS instead of using `font-family: "Font Awesome 5 Free OR Solids OR Brands";`


------------
    
    
## Answer \#8

 Vote: 3

Created at 2019-08-20 15:00:00

------------

Strangely you have to include the font-family and the font-weight for it to work. 
Here is what worked for me:

```
.second-section-header::after {
    content: "\f259";
    font-family: 'Font Awesome\ 5 Free';
    font-weight: 900;
}
```


From there, you can begin adding any styles that you want. 

Let's say: 

```
.second-section-header::after {
    content: "\f259";
    font-family: 'Font Awesome\ 5 Free';
    font-weight: 900;
    font-size: 100px;
    color: white;
    z-index: 1;
    position: absolute;
}
```


I hope this helps. 


------------
    
    
## Answer \#9

 Vote: 3

Created at 2020-08-24 16:21:24

------------

IF it's still not working for you, I had forgotten to add the `fa-ul` class onto the parent (UL) element:
```
<ul class="fa-ul">
```

Together with the 2 bits of advice provided already by others:
```
a) font-family: 'Font Awesome\ 5 Free';
b) font-weight: 900;
```

solved it for me.
FWIW, the include in my `<head>` tags is just:
```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.14.0/css/all.min.css" integrity="sha512-1PKOgIY59xJ8Co8+NE6FZ+LOAZKjy+KY8iq0G4B3CyeY6wYHN3yt9PW0XpSriVlkMXe40PTKnXrLnZ9+fkDaog==" crossorigin="anonymous" />
```

And I am using this with React and Preact. No need for any special React npm installs or components.


------------
    
    
## Answer \#10

 Vote: 2

Created at 2018-10-22 10:09:49

------------

npm i --save @fortawesome/fontawesome-free

My Sccs:

```
@import "~@fortawesome/fontawesome-free/scss/fontawesome";
@import "~@fortawesome/fontawesome-free/scss/brands";
@import "~@fortawesome/fontawesome-free/scss/regular";
@import "~@fortawesome/fontawesome-free/scss/solid";
@import "~@fortawesome/fontawesome-free/scss/v4-shims";
```


It worked fine for me!


------------
    
    
## Answer \#11

 Vote: 1

Created at 2018-06-01 21:09:20

------------

the solution is call it like normal font:

```
@font-face {
font-family: "Font Awesome 5 Free-Regular-400";
src: url(../fonts/Font%20Awesome%205%20Free-Regular-400.otf) format("opentype");}
```



------------
    
    
## Answer \#12

 Vote: 1

Created at 2019-06-26 06:11:36

------------

that's probably about pricing model... ;)

[https://fontawesome.com/how-to-use/on-the-web/referencing-icons/basic-use](https://fontawesome.com/how-to-use/on-the-web/referencing-icons/basic-use)

```
Solid    Free           fas   <i class="fas fa-camera"></i> 
Regular  Pro Required   far   <i class="far fa-camera"></i> 
Light    Pro Required   fal   <i class="fal fa-camera"></i> 
Brands   Free           fab   <i class="fab fa-font-awesome"></i>
```



------------
    
    
## Answer \#13

 Vote: 0

Created at 2017-12-14 11:40:23

------------

I found a solution.

- `fontawesome-all.css`- At the end of file Search the second @font-face and replacefont-family: 'Font Awesome 5 Free';

With

```
font-family: 'Font Awesome 5 FreeR';
```


And replace:

```
.far {
  font-family: 'Font Awesome 5 Free';
  font-weight: 400; }
```


With

```
.far {
  font-family: 'Font Awesome 5 FreeR';
  font-weight: 400; }
```



------------
    
    
## Answer \#14

 Vote: 0

Created at 2018-08-05 22:09:21

------------

If you are using  you need to enable this because it's disabled by default. Use 

`<script data-search-pseudo-elements ... >` 

inside your script tag.


------------
    
    
## Answer \#15

 Vote: 0

Created at 2022-03-14 14:28:09

------------

Add the `fas` class.
Like:
```
<i class="fas fa-solid fa-angle-left"></i>
```

Without the `fas` the icon will not be loaded


------------
    
    
## Answer \#16

 Vote: 0

Created at 2022-10-17 02:17:47

------------

If it's still not working for you you should be able to see the code line in your page source (Right Click -> select "View Page Source") where you have imported the FontAwesome stylesheet.
```
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.5.0/css/font-awesome.min.css">
```

If you click on that, it will open the stylesheet in a new tab. In the top part of the stylesheet you will be able to see the name of the font family.
[](https://i.stack.imgur.com/nFdv3.png)
Then you can change the font-family only for the icons which are breaking with the new font used in the web page.
```
#primary-menu .fa-angle-down:before {
    font-family: 'FontAwesome', sans-serif;
    /* Changing the icons to FontAwesome font to stop breaking when the font is changed in the site */
}
```



------------
    
    
## Answer \#17

 Vote: -1

Created at 2018-04-12 01:22:45

------------

I had to set `searchPseudoElements` to to true to get it working in Angular5.

```
import fontawesome from '@fortawesome/fontawesome';
...
fontawesome.config.searchPseudoElements = true;
...
content: "\f12a";
font-family: 'Font Awesome 5 Solid';
```



------------
    
    