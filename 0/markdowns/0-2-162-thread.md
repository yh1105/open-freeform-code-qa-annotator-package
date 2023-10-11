
# Post \#52637835 [Link](https://stackoverflow.com/questions/52637835/)

## Dynamically change background color on scroll

**Vote**: 16 (306/702) **Views**: 65305 (132/702) 

**Internal ID** \#0-2-162

Created at 2018-10-04 01:46:05

Tags: `javascript` `css` `dom-events`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

Is there any way to change background color dynamically on scroll?

For example, refer this site([https://www.samsung.com/sec/smartphones/galaxy-note9/](https://www.samsung.com/sec/smartphones/galaxy-note9/))

When you first access that site, background color is blue.

While scroll down, it's color change to black smoothly.

Also see this site(`codepen.io/Funsella/pen/yLfAG/`)

Second site is same with first. But it's color changed at once.

But first site's color is not change at once.

It changed gradually related to scroll position.

```
body {
  height: 100vh;
}
.section1 {
  background-color: white;
  height: 100%;
}
.section2 {
  background: linear-gradient(#f05fa6, #ed1654);
  height: 100%;
}
```
```
<html>
<body>
  <section class="section1">
    SECTION1
  </section>
  <section class="section2">
    SECTION2
  </section>
</body>
</html>
```



Above code is what I'm worked on.

Current it's color is split by each section.

When I scroll down, I want to change color `background-color: white` -> `background: linear-gradient(#f05fa6, #ed1654)`

Is there any solution about this?


----------
        
## Answer \#0

**Accepted** Vote: 18

Created at 2018-10-04 02:54:46

------------

You need to smoothly interpolate the colors by taking into account the page's scroll offset (`window.scrollY`, or `window.pageYOffset` on older browsers). 

The Samsung site is transitioning a solid color instead of a gradient, which is a bit simpler.

Like this ([see CodePen](https://codepen.io/atomiks/pen/dgMNwG)):

```
const [red, green, blue] = [69, 111, 225]
const section1 = document.querySelector('.section1')

window.addEventListener('scroll', () => {
  let y = 1 + (window.scrollY || window.pageYOffset) / 150
  y = y < 1 ? 1 : y // ensure y is always >= 1 (due to Safari's elastic scroll)
  const [r, g, b] = [red/y, green/y, blue/y].map(Math.round)
  section1.style.backgroundColor = `rgb(${r}, ${g}, ${b})`
})
```


You can apply the same logic to the gradient colors.


------------
    
    
## Answer \#1

 Vote: 4

Created at 2018-10-04 01:52:08

------------

I think you need to use the "transition" property of CSS.

```
body {
  background: green;
  transition: 0.3s all;
}
```


Then you can add, remove elements along with change color:

```
$(function() {
$(window).scroll(function () {
   if ($(this).scrollTop() > 50) {
      $(‘body’).addClass(‘colorChange’)
      $(‘header’).addClass(‘displayNone’)
      $(‘nav’).removeClass(‘navBackgroundStart’)
      $(‘nav ul’).addClass(‘addBlackBackground’)
   } 
   if ($(this).scrollTop() < 50) {
      $(‘body’).removeClass(‘colorChange’)
      $(‘header’).removeClass(‘displayNone’)
      $(‘nav’).addClass(‘navBackgroundStart’)
      $(‘nav ul’).removeClass(‘addBlackBackground’)
   } 
});
});
```



------------
    
    
## Answer \#2

 Vote: 4

Created at 2019-06-07 08:28:35

------------

I tried to use the solution of atomiks with a custom end color but it was too difficult. I found myself a better solution by using [chroma.js](https://gka.github.io/chroma.js)

You need to generate a scale with your two colors (or more) :

```
var scale = chroma.scale(['#1abc9c', '#e74c3c']).domain([0, $(document).height()]);
$(window).on('scroll', function () {
    $('.section').css('background-color', scale(window.pageYOffset));
});
```


Here I create a [scale](https://gka.github.io/chroma.js/#chroma-scale) with the color I want and then I add a custom domain so that my scale can use the offset position which can go from 0 (top of the page) 3600 (bottom of my page). Or, you can try to get the scroll position value between 0 and 1 with some math.

Then, when we scroll, we can use our scale with the current scroll position. It will generate a RGBA color between our two colors before reaching the last one at the bottom of the page `#e74c3c`


------------
    
    
## Answer \#3

 Vote: 2

Created at 2018-10-04 02:48:19

------------

This jQuery code changes the background color of the body.
```
$(document).ready(function() {
    var scroll_pos = 0;
    $(document).scroll(function() {
        scroll_pos = $(this).scrollTop();
        if(scroll_pos > 300) {
            $("body").css('background-color', 'blue');
        } else {
            $("body").css('background-color', 'red');
        }
    });
});
```

Don't forget to add the transition CSS to get that effect you see in your example.
```
body {
  -webkit-transition: all 0.5s ease;
  -moz-transition: all 0.5s ease;
  -o-transition: all 0.5s ease;
  transition: all 0.5s ease;
}
```

Make the backgrounds of your section classes transparent.
```
.section1, section2 {
  background-color: transparent;
}
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-03-28 13:19:27

------------


# This effect can be gotten with a bit of JavaScript


You first create a div with some content in it. You create a box that is smaller than the content and set the overflow property to scroll.
With a tiny bit of JavaScript you add the functionality. You first create an event Listener than triggers a function when the scroll effect is used. When that event Listener triggers you then dynamically set the background color to your requirements.
```
document.getElementById("myDIV").addEventListener("scroll", myFunction);

function myFunction() {
  document.getElementById("myDIV").style.backgroundColor = "red";
}
```
```
div {
  border: 1px solid black;
  width: 400px;
  height: 400px;
  overflow: scroll;
  transition: all 0.5s ease;
}
```
```
<p>Try the scrollbar in the div</p>
<div id="myDIV">Why do we use it?
It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout. The point of using Lorem Ipsum is that it has a more-or-less normal distribution of letters, as opposed to using 'Content here, content here', making it look like readable English. Many desktop publishing packages and web page editors now use Lorem Ipsum as their default model text, and a search for 'lorem ipsum' will uncover many web sites still in their infancy. Various versions have evolved over the years, sometimes by accident, sometimes on purpose (injected humour and the like).
<br><br>
Where does it come from?
Contrary to popular belief, Lorem Ipsum is not simply random text. It has roots in a piece of classical Latin literature from 45 BC, making it over 2000 years old. Richard McClintock, a Latin professor at Hampden-Sydney College in Virginia, looked up one of the more obscure Latin words, consectetur, from a Lorem Ipsum passage, and going through the cites of the word in classical literature, discovered the undoubtable source. Lorem Ipsum comes from sections 1.10.32 and 1.10.33 of "de Finibus Bonorum et Malorum" (The Extremes of Good and Evil) by Cicero, written in 45 BC. This book is a treatise on the theory of ethics, very popular during the Renaissance. The first line of Lorem Ipsum, "Lorem ipsum dolor sit amet..", comes from a line in section 1.10.32.</div>
```




------------
    
    