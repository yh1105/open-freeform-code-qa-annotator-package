
# Post \#53416348 [Link](https://stackoverflow.com/questions/53416348/)

## CSS Scroll Snapping, vertical not working

**Vote**: 24 (233/702) **Views**: 50754 (161/702) 

**Internal ID** \#0-1-136

Created at 2018-11-21 16:19:19

Tags: `css`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I used [this article](https://css-tricks.com/practical-css-scroll-snapping/) as a point of reference, in particular [this working snippet](https://codepen.io/maxakohler/pen/KBKNxd), but in my page (the script below) the vertical snap scrolling isn't working. Why?
```
.parent {
  height: 100vh;
  scroll-snap-type: mandatory;
  scroll-snap-points-y: repeat(100vh);
  scroll-snap-type: y mandatory;
}

section {
  height: 100vh;
  scroll-snap-align: start;
  position: relative;
}

.one {
  background-color: red;
}

.two {
  background-color: blue;
}

.three {
  background-color: grey;
}

.four {
  background-color: green;
}
```
```
<div class="parent row">
  <section class="one">
  </section>
  <section class="two">
  </section>
  <section class="three">
  </section>
  <section class="four">
  </section>
</div>
```




----------
        
## Answer \#0

**Accepted** Vote: 70

Created at 2018-11-21 17:22:06

------------

The major problem in the code snippet is that the displayed scrollbar belongs to the body, where no scroll-snap properties have been defined. This is why you do not have any snapping behaviour when scrolling.
To achieve your expected result, you need to

1. Be sure that the displayed scrollbar belongs to the parent div
2. Define the overflow behaviour to the parent container to scroll


Below is a working sample
As a note, consider that snapping properties (for Chrome) have evolved, and that you are using deprecated features. See the [CSS scroll snap on Google developers](https://developers.google.com/web/updates/2018/07/css-scroll-snap).
Note also that this answer deals only with Chrome, without the [polyfill](https://en.wikipedia.org/wiki/Polyfill_(programming)) part. It is just the main scroll concept that is involved here.
```
html,
body {
  height: 100vh;
  overflow: hidden;
}

.parent {
  overflow: scroll;
  height: 100vh;
  scroll-snap-points-y: repeat(100vh);
  scroll-snap-type: y mandatory;
}

section {
  height: 100vh;
  scroll-snap-align: start;
  position: relative;
}

.one {
  background-color: red;
}

.two {
  background-color: blue;
}

.three {
  background-color: grey;
}

.four {
  background-color: green;
}
```
```
<div class="parent row">
  <section class="one"></section>
  <section class="two"></section>
  <section class="three"></section>
  <section class="four"></section>
</div>
```




------------
    
    
## Answer \#1

 Vote: 12

Created at 2021-08-07 04:45:06

------------

[The original answer](https://stackoverflow.com/questions/53416348/css-scroll-snapping-vertical-not-working/53417516#53417516) says to put `overflow: hidden` directly on the `html` and `body` elements which messes up a lot of things, namely headers that use `position: sticky` which is a pretty common concept.
The alternative is to just put the `scroll-snap-type` property on the body and then put the `scroll-snap-align` property on the elements that need to have scroll snap behavior.
```
body, html {
  scroll-snap-type: proximity;
  scroll-snap-points-y: repeat(100vh);
  scroll-snap-type: y proximity;
}

section {
  height: 100vh;
}

.row section {
  scroll-snap-align: start;
}

.one {
  background-color: red;
}

.two {
  background-color: blue;
}

.three {
  background-color: grey;
}

.four {
  background-color: green;
}
```
```
<div class="row">
  <section class="one">
  </section>
  <section class="two">
  </section>
  <section class="three">
  </section>
  <section class="four">
  </section>
</div>
<section>
  <h2>This will not have sticky</h2>
</section>
```




------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-01-22 01:20:07

------------

The following is my current best attempt to work around the Chrome bug mentioned in the previous comments, using JavaScript. . I guess scrolling in browsers will never be as smooth as in file managers. That's life.
 I should add that many people will not like if a website changes in the middle how scrolling behaves. It may be a good idea to leave regions on the left and right where scrolling behaves normal. Please consider the below code example for what it is, an attempt to work around a bug in Chromes implementation of the CSS-only feature. (See my comments to the other answers)
```
let adjusted = false

function snapscroll(event) {
    let snappable = null; {
        let hovered = document.elementsFromPoint(event.clientX, event.clientY)
        for (elem of hovered) {
            if (elem.classList.contains('snapme')) {
                snappable = elem
                break
            }
        }
    }

    let prev = null
    let next = null
    if (snappable !== null) {
        prev = snappable.previousElementSibling; if (prev !== null && !prev.classList.contains('snapme')) {prev = null}
        next = snappable.nextElementSibling; if (next !== null && !next.classList.contains('snapme')) {next = null}
    }

    if (event.deltaY > 0) {
        if (next === null) {
            snappable = null
            adjusted = false
        } else {
            if (adjusted) {
                snappable = next
            }
        }
    } else {
        if (prev === null) {
            snappable = null
            adjusted = false
        } else {
            if (adjusted) {
                snappable = prev
            }
        }
    }


    if (snappable !== null) {
        snappable.scrollIntoView({behavior: "smooth"})
        adjusted = true
        event.preventDefault()
        event.stopPropagation()
    }
}

let doit
document.addEventListener('wheel', function(event) {
    clearTimeout(doit)
    doit = setTimeout(() => snapscroll(event), 25)
})
```
```
body {padding:1em;}
section { height: 100vh; }
.snapme {cursor:move;}
.one { background-color: red; }
.two { background-color: blue; }
.three { background-color: grey; }
.four { background-color: green; }
```
```
<section>
    <h2>No snapping here</h2>
</section>

<section class='snapme one'></section>
<section class='snapme two'></section>
<section class='snapme three'></section>
<section class='snapme four'></section>

<section>
    <h2>No snapping here</h2>
</section>
```




------------
    
    