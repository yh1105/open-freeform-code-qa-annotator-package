
# Post \#60635196 [Link](https://stackoverflow.com/questions/60635196/)

## animate line under two css tabs

**Vote**: 0 (696/702) **Views**: 11236 (403/702) 

**Internal ID** \#0-2-170

Created at 2020-03-11 11:40:52

Tags: `jquery` `css` `vue.js` `bootstrap-4`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

Please I wanna know how to create an animation like this one using JS or Jquery or Vue Transitions, (I am using bootstrap).

I want to move the line under the TAB1 to the right when I click on TAB2. how can I animate the div to move smoothly from left to right and verse-versa ?

[](https://i.stack.imgur.com/NK8zF.png)
This is my code:

```
<div class="col-md-12">
  <div class="col-md-6">
        <p>TAB1</p>
  </div>
  <div class="col-md-6">
        <p>TAB2</p>
  </div>
</div>
<div class="col-md-6">
<span style="height:10px;background-color:"></span>
</div>
```



----------
        
## Answer \#0

**Accepted** Vote: 7

Created at 2020-03-11 13:04:53

------------

I've made two different versions.

The first is a Vue specific one which also implements a hover effect, so that the bar moves when moving a tab, and goes back if the tab wasn't clicked.

The second example is a plain javascript version which only moves the bar when clicking on a new tab.

```
/* Vue Section */
new Vue({
  el: '#app',
  computed: {
    lineStyles() {
      return this.lineTemp == null ? this.line : this.lineTemp;
    }
  },
  mounted() {
    const tab = document.getElementById('nav-tab-1');
    if (tab) {
      let styles = {
        left: tab.offsetLeft,
        width: tab.clientWidth
      }
      this.line = styles;
    }
  },
  data() {
    return {
      currentTab: 2,
      line: {
        left: 0,
        width: 69
      },
      lineTemp: null
    }
  },
  methods: {
    onTabClick(evt) {
      const tab = evt.target
      let styles = {
        left: tab.offsetLeft,
        width: tab.clientWidth
      }
      this.line = styles;
    },
    onTabMouseEnter(evt) {
      const tab = evt.target
      let styles = {
        left: tab.offsetLeft,
        width: tab.clientWidth
      }
      this.lineTemp = styles;
    },
    onTabMouseLeave() {
      this.lineTemp = null;
    }
  }
})


/* Plain JS section */
function onTabClick(evt) {
  setLineStyle(evt.target)
}

function setLineStyle(tab) {
  let line = document.querySelector('.tabs2 > .line')
  line.style.left = tab.offsetLeft + "px";
  line.style.width = tab.clientWidth + "px";
}

/* bind events on load */
window.onload = function() {
  const tabs = document.querySelectorAll('.tabs2 > .nav > .nav-item')
  tabs.forEach((tab, index) => {
    tab.onclick = onTabClick;
    
    if(index == 0) setLineStyle(tab);
  })
}
```
```
.line {
  position: absolute;
  bottom: 0;
  transition: left 0.5s ease-in-out, width 0.5s 0.10s;
  height: 10px;
  background-color: blue;
  left: 0;
  width: 69px;
  pointer-events: none;
}
```
```
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.6.11/vue.js"></script>
<link href="https://unpkg.com/bootstrap@4.4.1/dist/css/bootstrap.min.css" rel="stylesheet" />

<!-- Vue Section -->
<div id="app">
  <h4>Vue Option with hover effect</h4>
  <div class="position-relative">
    <ul class="nav">
      <li v-for="i in 3" :id="`nav-tab-${i}`" class="nav-item" @click="onTabClick" @mouseenter="onTabMouseEnter" @mouseleave="onTabMouseLeave">
        <a class="nav-link" href="#">
          Tab {{ i }}
        </a>
      </li>
    </ul>
    <div class="line" :style="{ left: `${lineStyles.left}px`, width: `${lineStyles.width}px` }"></div>
  </div>
</div>
<hr />

<!-- Plain Javascript Section -->
<div>
  <h4>Plain javascript option with only click</h4>
  <div class="tabs2 position-relative">
    <ul class="nav">
      <li class="nav-item">
        <a class="nav-link" href="#">
          Tab 1
        </a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">
          Long tab 2
        </a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">
          Tab 3
        </a>
      </li>
    </ul>
    <div class="line"></div>
  </div>
</div>
```




------------
    
    
## Answer \#1

 Vote: 4

Created at 2020-03-11 12:11:00

------------

Please try this one code below. I hope you will get your solution including animation.
```
.tab p {
    display: inline-block;
    margin-right: 30px;
    position: relative;
    margin-bottom: 0;
    font-size: 30px;
}
    
.tab p:last-child {
    margin-right: 0px;
}
    
.tab p:before {
    content: '';
    position: absolute;
    left: 0;
    bottom: 0;
    width: 0;
    height: 5px;
    background-color: #8C076E;
    -webkit-transition: .5s;
    transition: .5s
}
    
.tab p:hover:before {
    width: 100%
}
```
```
<div class="col-md-12">
    <div class="tab">
        <p>TAB1</p>
        <p>TAB2</p>
    </div>
</div>
```




------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-08-22 04:21:36

------------

Try this, it's my home made may help, I'm not using a bootstrap, just a simple way:
```
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
      /* mymenu container can be anything you want */
      .menuwrap {
        margin-top: 0px; 
        padding:0px; 
        background-color: white ; 
        overflow: auto; 
        white-space: nowrap; 
        width: 100%; 
        height: 75px;
      }

      .mylinec { 
        margin-left: 0px; 
        top: 0px; 
        position: relative; 
        background-color: blue; 
        height: 5px; 
        width:100px; 
      } 

      .mybutton {
        width:100px;
        background: transparent; 
        border:0px; 
        color: black; 
        padding: 10px 15px; 
        text-align: center; 
        text-decoration: none; 
        display: inline-block; 
        font-size: 13px; 
        margin: 0px 0px; 
        cursor: pointer; 
        -webkit-transition-duration: 0.4s; 
        /* Safari */ 
        transition-duration: 0.4s; 
        //if need shadow; 
        //box-shadow: 0px 5px 18px
      }
    </style>
  </head>
  <body style="text-align:left; margin: 0px;">
    <div class="menuwrap" id="menu">
      <input type="button" class="mybutton" id="tab1" value="Tab 1" onClick="req_divmleft(this.offsetLeft), animatethisline()"/>
      <input type="button" class="mybutton" id="tab2" value="Tab 2" onClick="req_divmleft(this.offsetLeft), animatethisline()"/>
      <input type="button" class="mybutton" id="tab3" value="Tab 3" onClick="req_divmleft(this.offsetLeft), animatethisline()"/>
  
      <div id="myline" class ="mylinec"></div>
    </div>

    <!-- Need storage or variable for the history click or this can be hide you change type="hidden"  -->

    <input type="text" class="" id="txtstart" value="0" />
    <input type="text" class="" id="txtdestination" value="0" />

    <script>
      // request elements left
      function req_divmleft(clicked_offsetLeft) { 
        document.getElementById("txtdestination").value = clicked_offsetLeft; 
      }

      function animatethisline() {
        // get delay var left from left start
        var getdlm = document.getElementById("txtstart");
        // get destination (active moves)
        var getlm = document.getElementById("txtdestination");
        var leslide = document.getElementById("myline");

        leslide.animate([ 
          // keyframes 
          { transform: "translateX("+getdlm.value+"px)" }, 
          { transform: "translateX("+getlm.value+"px)" }
        ], { // timing options 
          duration: 1000, 
          easing: "ease-out",
          fill: "both"
        });

        setTimeout(getdelx, 100);

        function getdelx() {
          getdlm.value = getlm.value;
        }
      }
    </script>
  </body>
</html>
```




------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-03-11 13:41:08

------------

```
.navbar {
            list-style: none;
            margin: 50px auto;
            max-width: 720px;
            padding: 0;
            width: 100%;
        }

        .navbar-item {
            background: #fff;
            display: block;
            float: left;
            margin: 0;
            padding: 0;
            width: 20%;
            text-align: center;
        }

        .navbar-item:first-child {
            border-radius: 3px 0 0 3px;
        }

        .navbar-item:last-child {
            border-radius: 0 3px 3px 0;
        }

        .navbar-item.navbar-item-active a {
            color: #e80000;
        }

        .navbar-item a {
            color: #313131;
            display: block;
            padding-top: 20px;
            padding-bottom: 20px;
            text-decoration: none;
        }

        .navbar-item a:hover {
            color: #e80000;
        }

        .navbar-indicator {
            position: relative;
            z-index: 0;
        }

        .navbar-indicator .navbar-item:last-child:before,
        .navbar-indicator .navbar-item:last-child:after {
            content: '';
            display: block;
            position: absolute;
            pointer-events: none;
            -webkit-transition: left 1.3s ease;
            transition: left 1.3s ease;
        }

        .navbar-indicator .navbar-item:last-child:before {
            border: 6px solid transparent;
            border-top-color: #e80000;
            width: 0;
            height: 0;
            top: 0;
            left: 10%;
            margin-left: -3px;
        }

        .navbar-indicator .navbar-item:last-child:after {
            background: #e80000;
            top: -6px;
            bottom: -6px;
            left: 0;
            width: 20%;
            z-index: -1;
        }

        .navbar-indicator .navbar-item:nth-child(1).navbar-item-active~.navbar-item:last-child:after {
            left: 0%;
        }

        .navbar-indicator .navbar-item:nth-child(1).navbar-item-active~.navbar-item:last-child:before {
            left: 10%;
        }

        .navbar-indicator .navbar-item:nth-child(2).navbar-item-active~.navbar-item:last-child:after {
            left: 20%;
        }

        .navbar-indicator .navbar-item:nth-child(2).navbar-item-active~.navbar-item:last-child:before {
            left: 30%;
        }

        .navbar-indicator .navbar-item:nth-child(3).navbar-item-active~.navbar-item:last-child:after {
            left: 40%;
        }

        .navbar-indicator .navbar-item:nth-child(3).navbar-item-active~.navbar-item:last-child:before {
            left: 50%;
        }

        .navbar-indicator .navbar-item:nth-child(4).navbar-item-active~.navbar-item:last-child:after {
            left: 60%;
        }

        .navbar-indicator .navbar-item:nth-child(4).navbar-item-active~.navbar-item:last-child:before {
            left: 70%;
        }

        .navbar-indicator .navbar-item:nth-child(1):hover~.navbar-item:last-child:after {
            left: 0% !important;
        }

        .navbar-indicator .navbar-item:nth-child(1):hover~.navbar-item:last-child:before {
            left: 10% !important;
        }

        .navbar-indicator .navbar-item:nth-child(2):hover~.navbar-item:last-child:after {
            left: 20% !important;
        }

        .navbar-indicator .navbar-item:nth-child(2):hover~.navbar-item:last-child:before {
            left: 30% !important;
        }

        .navbar-indicator .navbar-item:nth-child(3):hover~.navbar-item:last-child:after {
            left: 40% !important;
        }

        .navbar-indicator .navbar-item:nth-child(3):hover~.navbar-item:last-child:before {
            left: 50% !important;
        }

        .navbar-indicator .navbar-item:nth-child(4):hover~.navbar-item:last-child:after {
            left: 60% !important;
        }

        .navbar-indicator .navbar-item:nth-child(4):hover~.navbar-item:last-child:before {
            left: 70% !important;
        }

        .navbar-indicator .navbar-item:last-child:hover:before,
        .navbar-indicator .navbar-item:last-child.navbar-item-active:before {
            left: 90% !important;
        }

        .navbar-indicator .navbar-item:last-child:hover:after,
        .navbar-indicator .navbar-item:last-child.navbar-item-active:after {
            left: 80% !important;
        }

        *,
        *:before,
        *:after {
            box-sizing: border-box;
        }

        .navbar:before,
        .navbar:after {
            content: " ";
            display: table;
        }

        .navbar:after {
            clear: both;
        }

        html {
            background-color: #313131;
            font-family: 'Open Sans', sans-serif;
            font-weight: 800;
        }

        .toggle {
            color: #fff;
            font-family: sans-serif;
            text-align: center;
        }
```
```
<!doctype html>
<html lang="en">

<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>css animate border left to right - tabmenu</title>
</head>
<body>
    <ul class="navbar navbar-indicator">
        <li class="navbar-item"><a href="#">Menu 1</a></li>
        <li class="navbar-item"><a href="#">Menu 2</a></li>
        <li class="navbar-item navbar-item-active"><a href="#">Menu 3</a></li>
        <li class="navbar-item"><a href="#">Menu 4</a></li>
        <li class="navbar-item"><a href="#">Menu 5</a></li>
    </ul>
</body>

</html>
```




------------
    
    
## Answer \#4

 Vote: 0

Created at 2020-03-11 12:03:16

------------

if you want to do that use a `div` tag with `position:relative` under the both buttons and
then make a child of that div with your required width and give it `position:absolute`.And simply adjust the left and right values on hover and focus or your required pseudo class hope you understand it


------------
    
    
## Answer \#5

 Vote: 0

Created at 2020-03-11 12:07:28

------------

I have used this simple cross-browser css library for creating lot of animations.
Hope this will help you to solve your problem.
(you can make a div out side of your text and animate it's bottom border to move smoothly from left to right)
just use this and make youe animation

[animate.css](https://daneden.github.io/animate.css/)

[animate.css > github](https://github.com/daneden/animate.css)


------------
    
    
## Answer \#6

 Vote: 0

Created at 2020-03-11 13:41:27

------------

You can use this simple example for your reference, which is using jQuery.

```
$(document).on('click', '.slider-tabs .nav-link', function() {
  handleTabChange($(this));
});

handleTabChange($('.slider-tabs .active'));

function handleTabChange(tab) {
  var nav = tab.closest('.nav');
  $('.indicator', nav).css({
    width: tab.outerWidth(),
    left: tab.position().left
  });
  tab.siblings().removeClass('active');
  tab.addClass('active');
}
```
```
.slider-tabs.nav {
  position: relative;
  margin: 10px;
}

.slider-tabs.nav .nav-link.active {
  background: transparent !important;
  color: #8b076e;
}

.slider-tabs.nav .indicator {
  position: absolute;
  top: 100%;
  min-width: 0;
  width: 0;
  height: 5px;
  background: red;
  transition: left .3s, width .3s;
}
```
```
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">

<nav class="nav nav-pills slider-tabs">
  <a class="text-sm-center nav-link active" href="#">Active</a>
  <a class="text-sm-center nav-link" href="#">Products</a>
  <a class="text-sm-center nav-link" href="#">Services</a>
  <a class="text-sm-center nav-link" href="#">Link</a>
  <a class="text-sm-center nav-link disabled" href="#">Disabled</a>
  <div class="indicator"></div>
</nav>
```




------------
    
    