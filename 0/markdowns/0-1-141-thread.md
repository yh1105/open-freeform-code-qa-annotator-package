
# Post \#69422296 [Link](https://stackoverflow.com/questions/69422296/)

## Using Flex CSS and Bootstrap for a responsive layout

**Vote**: 5 (499/702) **Views**: 2308 (595/702) 

**Internal ID** \#0-1-141

Created at 2021-10-03 05:24:53

Tags: `css` `twitter-bootstrap` `flexbox` `bootstrap-5`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

I have been experimenting with full responsive layouts between desktops, tablet, and mobile phone sizes for a bit but am having trouble with the layout below.
I was using a grid layout from bootstrap but since it is two columns, as the width shrinks, the second column of two  goes below the left, main content section. I want to split it up as the screen caps below state.
This is the starting view:
[](https://i.stack.imgur.com/4XVfh.png)
This is what I want to happen:
[](https://i.stack.imgur.com/jLJ14.png)
This would be the mobile phone view:
[](https://i.stack.imgur.com/zUL8l.png)
So the wide view will get shrunk during testing and I want it to jump to the mobile view where the top side bar goes above the main content and matches the width of the main content while the bottom sidebar does the same thing to the bottom.
It is easy to put both sidebars on the bottom but I want to try to figure out the possibility of splitting it up.
For testing I am using flexbox, css3 and bootstrap5+ and no plugins or javascript.


----------
        
## Answer \#0

**Accepted** Vote: 4

Created at 2021-12-14 03:51:57

------------

 You can use CSS [float](https://developer.mozilla.org/en-US/docs/Web/CSS/float) feature with `float-end` and `float-start` bootstrap classes [ref](https://getbootstrap.com/docs/5.1/utilities/float/):
```
<link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" />
<div class="container-fluid vh-100 vw-100 py-2 bg-light clearfix">
  <div class="float-sm-end   col-sm-4 col-12 h-25  bg-secondary">Sidebar Top</div>
  <div class="float-sm-start col-sm-8 col-12 h-100 bg-warning">Content</div>
  <div class="float-sm-end   col-sm-4 col-12 h-25  bg-info">Sidebar bottom</div>
</div>
```


Go in `Full page` view and reduce browser width below 576px to see the responsive change.
I've used breakpoint small `col-sm-*` here, use different [breakpoints](https://getbootstrap.com/docs/5.0/layout/breakpoints/) to handle [different devices](https://www.mydevice.io/#compare-devices).
There are only `d-*-grid` rules in entire boostrap v5.1.3 CSS file. Rest of the stuff is [experimental](https://getbootstrap.com/docs/5.1/layout/css-grid/).

---


 Using flexbox, playing with order and wrap. Improved version of @Rana's answer:
```
<link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" />
<div class="d-flex flex-column flex-sm-wrap vh-100 vw-100 py-2 bg-light">
  <div class="order-sm-1 order-2 col-sm-8 col-12 h-100 bg-warning">Content</div>
  <div class="order-sm-2 order-1 col-sm-4 col-12 h-25  bg-secondary">Sidebar Top</div>
  <div class="order-sm-3 order-3 col-sm-4 col-12 h-25  bg-info">Sidebar bottom</div>
</div>
```




------------
    
    
## Answer \#1

 Vote: 2

Created at 2021-10-03 05:27:58

------------

You may want to try out Bootstrap's Flex order utility to achieve the desired result
[https://getbootstrap.com/docs/5.0/utilities/flex/#order](https://getbootstrap.com/docs/5.0/utilities/flex/#order)
Also, share a code example so others can see what you have already tried, I believe you may face some issues even with Flex's Order utility, but that depends on your HTML structure.
For such layout my preference would be to create a copy of the top column for mobile / desktop, and use `d-` utility to show hide the right one.
```
<div class="container">
    
    <div class="row ">
      
      <div class="col-12 d-md-none d-block">
        <div class="box box-1">sidebar top col (only for mobile)</div>
      </div>
      
      <div class="col-md-8">
        <div class="box box-2">main col</div>
      </div>
      
      <div class="col-md-4">
        <div class="box box-3 d-none d-md-block">sidebar top col (only for desktop)</div>
        <div class="box box-4">sidebar bottom col</div>
      </div>
      
      
    </div>
  </div>
```

Example - [https://jsbin.com/yidokokewu/1/edit?html,css,output](https://jsbin.com/yidokokewu/1/edit?html,css,output)
Alternatively if you are already using Bootstrap 5, then you should also consider using CSS Grid, Bootstrap 5 comes with a lot of useful utilities for that -
[https://getbootstrap.com/docs/5.1/layout/css-grid/](https://getbootstrap.com/docs/5.1/layout/css-grid/)
If you do not want to duplicate your HTML to show one on desktop and the other on mobile then CSS Grid for layout is the best choice - it has some learning curve but it is worth trying out


------------
    
    
## Answer \#2

 Vote: 2

Created at 2021-12-17 06:26:58

------------

You can try following properties
```
flex-flow: column;
flex-wrap: wrap/nowrap;
order: 1/2/3/....;
```

When screen-size reaches certain limit than change these values like in below snippet
```
.container {
  display: flex;
  flex-flow: column;
  flex-wrap: wrap;
  background-color: lightgray;
}

.containerBox {
  width: 50%;
}

.containerBox1 {
  background-color: lightgreen;
  order: 2;
}

.containerBox2 {
  background-color: yellow;
  order: 1;
}

.containerBox3 {
  background-color: lightblue;
  order: 3;
}

@media screen and (max-width: 700px) {
  .container {
    background-color: grey;
    flex-wrap: nowrap;
  }
  .containerBox {
    width: 100%;
  }
  .containerBox1 {
    order: 1;
  }
}
```
```
<link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet" />
<div class="container vh-100 vw-100">
  <div class="containerBox containerBox1 h-25">Sidebar Top</div>
  <div class="containerBox containerBox2 h-100">Content</div>
  <div class="containerBox containerBox3 h-25">Sidebar bottom</div>
</div>
```




------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-12-14 19:34:00

------------

Using `float-start` for left column and `float-end` for right columns is the best solution. `col-12` and `col-sm-*` were used only in order to defining width of columns. Avoid of using `row` class for parent div because this will force columns to act like flex items.
Also `clearfix` class for parent div is required to prevent overlapping following elements.
More information about float: [https://getbootstrap.com/docs/5.1/utilities/float/](https://getbootstrap.com/docs/5.1/utilities/float/)
More information about clear-fix: [https://getbootstrap.com/docs/5.1/helpers/clearfix/](https://getbootstrap.com/docs/5.1/helpers/clearfix/)
```
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
<div class="clearfix">
    <div class="col-12 col-sm-4 float-end bg-secondary">Sidebar Top Area</div> 
    <div class="col-12 col-sm-8 float-start bg-primary">Content<br>Some Text<br>Some Text</div>
    <div class="col-12 col-sm-4 float-end bg-success">Sidebar Bottom Area</div> 
</div>
```



------------
    
    
## Answer \#4

 Vote: 1

Created at 2021-12-18 06:40:24

------------

go to [enter link description here](https://www.w3schools.com/bootstrap5/bootstrap_grid_examples.php)
or
[enter link description here](https://getbootstrap.com/docs/5.1/layout/grid/)
```
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Bootstrap Example</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.2/dist/css/bootstrap.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.2/dist/js/bootstrap.bundle.min.js"></script>
</head>
<body>

<div class="container-fluid mt-3">     
  <div class="container-fluid">     
    <div class="row">
      <div class="col-12 col-sm-9 bg-success">col-12 col-sm-9</div>
      <div class="col-12 col-sm-3 bg-warning">col-12 col-sm-3</div>
    </div>
  </div>
</div>

</body>
</html>
```




------------
    
    
## Answer \#5

 Vote: 0

Created at 2021-12-18 15:02:29

------------

```
.content {
  background-color: #faaa03;
  height: 500px;
}
.sidebarTop {
  background-color: #ccd0d5;
}
.sidebarBottom {
  background-color: #99d2f2;
}
.custom-row {
  margin-right: calc(-0.5 * var(--bs-gutter-x));
  margin-left: calc(-0.5 * var(--bs-gutter-x));
}
```
```
<link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.1.3/css/bootstrap.min.css" rel="stylesheet"/>
<div class="container">
  <div class="custom-row clearfix d-flex flex-wrap d-lg-block">
    <div class="col-12 col-md-8 col-lg-8 content order-2 float-lg-start">Content</div>
    <div class="col-12 col-md-2 col-lg-4 sidebarTop order-1 float-lg-end">Sidebar Top Area</div>
    <div class="col-12 col-md-2 col-lg-4 sidebarBottom order-3 float-lg-end">Sidebar Bottom Area</div>
  </div>
</div>
```


Give this a Try


------------
    
    