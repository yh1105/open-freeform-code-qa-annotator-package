
# Post \#57516373 [Link](https://stackoverflow.com/questions/57516373/)

## Image stretching in flexbox in Safari

**Vote**: 74 (84/702) **Views**: 43378 (183/702) 

**Internal ID** \#0-1-135

Created at 2019-08-15 21:18:45

Tags: `css` `image` `safari` `flexbox` `height`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

This is only an issue in Safari and looks like a Safari bug to me. Here is a [fiddle](https://jsfiddle.net/0scrgd9j/) with a simplified version of the issue.

When an image is in a nested flexbox element with a width set and `height: auto` it is being stretched... the auto height is not working. Does something extra need to be added for this to work in Safari?

```
.container {
  display: flex;
  flex-direction: column;
}

.container section:first-child {
  display: flex;
  margin-bottom: 25px;
}

.container img {
  width: 125px;
  height: auto;
}
```
```
<div class="container">
  <section>
    <img src="https://via.placeholder.com/250">
  </section>
  <section>
    <img src="https://via.placeholder.com/150">
  </section>
</div>
```



I expect the height of the image to automatically be adjusted to maintain aspect ratio. This works in all browsers except Safari. In Safari the image is stretched and the auto height does not work.


----------
        
## Answer \#0

**Accepted** Vote: 140

Created at 2019-08-16 17:48:52

------------

It certainly appears to be a bug.

The default setting for [the align-items property](https://developer.mozilla.org/en-US/docs/Web/CSS/align-items) is `stretch`. Most major browsers handle this sensibly, stretching the image .

For whatever reason, Safari stretches the image to its natural height, taking the container along for the ride.


---




## flex-direction: row



To fix the problem, override the `stretch` default value with `flex-start` in the `align-items` property.

```
.container {
  display: flex;
  flex-direction: column;
}
.container section:first-child {
  display: flex;
  align-items: flex-start; /* new */
  margin-bottom: 25px;
}
.container img {
  width: 125px;
  height: auto;
}
```
```
<div class="container">
  <section>
    <img src="http://i.imgur.com/60PVLis.png">
  </section>
  <section>
    <img src="http://i.imgur.com/60PVLis.png">
  </section>
</div>
```




## jsFiddle demo




---




## flex-direction: column



Switching the direction of the flex container to `column` also fixes the problem. This works because `align-items` now applies to width  you've defined a width on the image.

If you reverse the image dimensions from

```
.container img {
   width: 125px;
   height: auto;
}
```


to

```
.container img {
   width: auto;
   height: 125px;
}
```


... you'll have the same problem in Safari as in `flex-direction: row`, and need `align-items: flex-start` for the correction.

```
.container {
  display: flex;
  flex-direction: column;
}

.container section:first-child {
  display: flex;
  /* align-items: flex-start; */
  margin-bottom: 25px;
}

.container img {
  width: auto;
  height: 125px;
}
```
```
<div class="container">
  <section>
    <img src="http://i.imgur.com/60PVLis.png">
  </section>
  <section>
    <img src="http://i.imgur.com/60PVLis.png">
  </section>
</div>
```




## jsFiddle demo




------------
    
    
## Answer \#1

 Vote: 13

Created at 2020-09-28 19:43:39

------------

Adding height: intrinsic; works for me to fix the stretched height in safari. Add it to the image itself.  Not the wrapper.  You will still need height: auto for the other browsers.


------------
    
    
## Answer \#2

 Vote: 5

Created at 2019-08-16 06:33:41

------------

See my demo for a working example, add `flex-direction: column;` to fix this issue.

```
.container {
  display: flex;
  flex-direction: column;
}

.container section:first-child {
  display: flex;
  flex-direction: column;
  margin-bottom: 25px;
}

.container img {
  width: 125px;
  height: auto;
}
```
```
<div class="container">
  <section>
    <img src="https://via.placeholder.com/250">
  </section>
  <section>
    <img src="https://via.placeholder.com/150">
  </section>
</div>
```




------------
    
    
## Answer \#3

 Vote: 4

Created at 2021-08-07 19:16:12

------------

For me neither of the solutions worked. I already had both `flex-direction: column` and `aligh-items: center`, although, in my case, I also had some other elements in the same flex container, alongside the image. Not sure if it had any impact.
What actually fixed the issue in my case was simply wrapping the image with a div:
```
<section>
    <div>
        <img src="https://via.placeholder.com/250">
    </div>
</section>
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2020-12-08 19:25:24

------------

if parent `<img/>` tag has `display:flex;` add `align-items: center;`
```
<div style="display:flex;align-items: center;">
    <img "img.jpg"/>
</div>
```



------------
    
    
## Answer \#5

 Vote: 1

Created at 2021-03-18 00:38:08

------------

For anyone looking over this post in the future: I had this problem with a fixed height `flex-direction:row` flexbox and images inside of flex-children with `height:100%`. The highest-voted solution wasn't enough to fix it.
For whatever reason, what ultimately fixed it in my case was adding `display:block` directly to the images.


------------
    
    