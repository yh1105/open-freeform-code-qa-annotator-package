
# Post \#55281672 [Link](https://stackoverflow.com/questions/55281672/)

## Make plus symbol in CSS

**Vote**: 11 (370/702) **Views**: 40561 (193/702) 

**Internal ID** \#0-2-171

Created at 2019-03-21 13:36:01

Tags: `html` `css` `css-shapes`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I have the below CSS code which gives the  symbol but not matches the design basically it needs to be thin. See snippet and [codpen](https://codepen.io/anon/pen/ywQOqQ)

```
.plus {
  position:relative;
  border: 1px dotted white;
  width: 3px;
  height: 3px;
  background-color: black;
  box-sizing: border-box;
  transform: scale(11);
}
```
```
<div class="plus"></div>
```



Should Look like the below symbol: [](https://i.stack.imgur.com/Uc3zT.png)

Any other styling is also fine for me but should look like the snapshot.


----------
        
## Answer \#0

**Accepted** Vote: 34

Created at 2019-03-21 13:43:33

------------

We can achieve this using one gradient like below:
```
.plus {
  --b: 4px; /* the thickness */
  width: 40px; /* the size */
  aspect-ratio: 1;
  border: 10px solid #000; /* the outer space */
  background:
    conic-gradient(from 90deg at var(--b) var(--b),#000 90deg,#fff 0) 
    calc(100% + var(--b)/2) calc(100% + var(--b)/2)/
    calc(50%  + var(--b))   calc(50%  + var(--b));
  display: inline-block;
}

.alt {
  border: none;
  margin: 10px;
  background:
    conic-gradient(from 90deg at var(--b) var(--b),#fff 90deg,#000 0) 
    calc(100% + var(--b)/2) calc(100% + var(--b)/2)/
    calc(50%  + var(--b))   calc(50%  + var(--b));
}
.radius {
  border-radius: 50%;
}
```
```
<div class="plus">
</div>

<div class="plus alt">
</div>

<div class="plus radius">
</div>
```



---



Use multiple background like below:
```
.plus {
  display:inline-block;
  width:50px;
  height:50px;
  
  background:
    linear-gradient(#fff 0 0),
    linear-gradient(#fff 0 0),
    #000;
  background-position:center;
  background-size: 50% 2px,2px 50%; /*thickness = 2px, length = 50% (25px)*/
  background-repeat:no-repeat;
}

.alt {
  background:
    linear-gradient(#000 0 0),
    linear-gradient(#000 0 0);
  background-position:center;
  background-size: 50% 2px,2px 50%; /*thickness = 2px, length = 50% (25px)*/
  background-repeat:no-repeat;
}
.radius {
  border-radius:50%;
}
```
```
<div class="plus">
</div>

<div class="plus alt">
</div>

<div class="plus radius">
</div>
```


[](https://i.stack.imgur.com/IePvj.png)
And here is with transparency:
```
.plus {
  width:50px;
  height:50px;
  display:inline-block;
  
  background:
    linear-gradient(#000 0 0) top left,
    linear-gradient(#000 0 0) top right,
    linear-gradient(#000 0 0) bottom left,
    linear-gradient(#000 0 0) bottom right;
  background-size: calc(50% - 1px) calc(50% - 1px); /*thickness = 2px (2*1px) */
  background-repeat:no-repeat;
  border:10px solid #000; /*length = 30px (50px - 2x10px) */
  box-sizing:border-box;
}

.radius {
  border-radius:50%;
}

body {
 background:pink;
}
```
```
<div class="plus">
</div>

<div class="plus radius">
</div>
```


[](https://i.stack.imgur.com/sZQ9p.png)
We can add CSS variable to easily control the overal shape:
```
.plus {
  --t:2px;   /* Thickness */
  --l:40px;  /* size of the symbol */
  --s:10px;  /* space around the symbol */
  --c1:#fff; /* Plus color*/
  --c2:#000; /* background color*/

  display:inline-block;
  width:var(--l);
  height:var(--l);
  padding:var(--s);
  box-sizing:border-box; /*Remove this if you don't want space to be included in the size*/
  
  background:
    linear-gradient(var(--c1) 0 0) content-box,
    linear-gradient(var(--c1) 0 0) content-box,
    var(--c2);
  background-position:center;
  background-size: 100% var(--t),var(--t) 100%;
  background-repeat:no-repeat;
}

.radius {
  border-radius:50%;
}
```
```
<div class="plus"></div>
<div class="plus" style="--l:35px;--t:3px;--c2:green"></div>
<div class="plus" style="--l:50px;--t:1px;--s:5px;--c1:red;"></div>
<div class="plus" style="--l:35px;--t:5px;--s:0px;--c1:blue;--c2:orange;"></div>

<br>
<div class="plus radius"></div>
<div class="plus radius" style="--l:35px;--t:3px;--c2:green"></div>
<div class="plus radius" style="--l:50px;--t:1px;--s:5px;--c1:red;"></div>
<div class="plus radius" style="--l:35px;--t:5px;--s:0px;--c1:blue;--c2:orange;"></div>
```


[](https://i.stack.imgur.com/e2uzu.png)


------------
    
    
## Answer \#1

 Vote: 3

Created at 2019-03-21 13:43:50

------------

I would recommend working with before and after pseudo elements to achieve this.

Basically I used the div as black background only, used the before element as vertical line and the after element as horizontal line.

```
.plus {
  position: relative;
  width:20px;
  height:20px;
  background:#000;
}

.plus:before,
.plus:after {
  content: "";
  position:absolute;
  background:#fff;
}

/* the vertical line */
.plus:before {
  left:50%;
  top:4px; /* this defines how much black "border" there should be */
  bottom:4px;
  width:2px;
  transform:translateX(-50%);
}

/* the horizontal line */
.plus:after {
  top:50%;
  left:4px;
  right:4px;
  height:2px;
  transform:translateY(-50%);
}
```


Here is is a full example: [https://codepen.io/Fitzi/pen/zbMBVw](https://codepen.io/Fitzi/pen/zbMBVw)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2019-03-21 13:44:10

------------

You could do this by using the `content` property of CSS:

```
.plus {
  height: 24px;
  width: 24px;
  display: inline-block;
  background-color: black;
  color: white;
  font-size: 24px;
  line-height: 24px;
  text-align: center;
}

.plus::before {
  content: "+";
}
```


Here's a [Fiddle](https://jsfiddle.net/Moonbird_IT/q64d7z1y/4/) showing the above code.


------------
    
    
## Answer \#3

 Vote: 1

Created at 2019-03-21 13:49:21

------------

I'd also advise using the actual symbol:

```
.plus {
  display: block;
  height: 0.6em;
  width: 0.6em;
  font-size: 100px;
  text-align: center;
  line-height: 0.5em;
  margin: 0;
  padding: 0;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  font-family: Frutiger, "Frutiger Linotype", Univers, Calibri, "Gill Sans", "Gill Sans MT", "Myriad Pro", Myriad, "DejaVu Sans Condensed", "Liberation Sans", "Nimbus Sans L", Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-weight: lighter;
  color: #ffffff;
  background-color: #000000;
}

.plus::before {
  display: block;
  content: '+';
}
```
```
<div class="plus"></div>
```



Fiddle: [https://jsfiddle.net/m5de0ycL/](https://jsfiddle.net/m5de0ycL/)

Then just change the font-size for size, and if it's not thin enough for you then you can change the font family to a narrower one.

[Make plus symbol in CSS](https://stackoverflow.com/questions/55281672/make-plus-symbol-in-css/55281827#55281827)


------------
    
    
## Answer \#4

 Vote: -1

Created at 2022-08-24 09:28:06

------------

```
.plus::before {
    position: absolute;
    content: "+";
    font-size: 20px;
    line-height: 15px;
    background-color: black;
    color: white;
    width: 20px;
    height: 20px;
    right: 0;
    top: 0;
    cursor: pointer;
    pointer-events: none;
}
```




------------
    
    