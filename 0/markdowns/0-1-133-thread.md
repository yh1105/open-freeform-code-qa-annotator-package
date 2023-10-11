
# Post \#41513463 [Link](https://stackoverflow.com/questions/41513463/)

## Bootstrap align navbar items to the right

**Vote**: 469 (1/702) **Views**: 833637 (2/702) 

**Internal ID** \#0-1-133

Created at 2017-01-06 20:02:38

Tags: `css` `twitter-bootstrap` `bootstrap-4` `flexbox` `bootstrap-5`

----------

#### Metadata:

Area: `Front-end`

Language: `css` (full parsed tag list: `css`)

----------

**Notepad**


----------

How do I align a navbar item to right?

I want to have the login and register to the right.
But everything I try does not seem to work.

[](https://i.stack.imgur.com/G2o6H.png)


## This is what I have tried so far:



- `<div>``<ul>``style="float: right"`- `<div>``<ul>``style="text-align: right"`- `<li>`- `!important`- `nav-item``nav-right``<li>`- `pull-sm-right``<li>`- `align-content-end``<li>`


## This is my code:



```
<div id="app" class="container">
  <nav class="navbar navbar-toggleable-md navbar-light bg-faded">
    <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <a class="navbar-brand" href="#">Navbar</a>
    <ul class="navbar-nav">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Features</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Pricingg</a>
      </li>
    </ul>
    <ul class="navbar-nav " >
      <li  class="nav-item">
        <a class="nav-link" href="{{ url('/login') }}">Login</a>
      </li>
      <li  class="nav-item">
        <a class="nav-link" href="{{ url('/register') }}">Register</a>
      </li>
    </ul>
  </nav>
  @yield('content')
</div>
```



----------
        
## Answer \#0

**Accepted** Vote: 776

Created at 2017-01-06 20:25:14

------------

 (update 2021)
In Bootstrap 5 ([see this question](https://stackoverflow.com/questions/65253543/align-nav-items-to-right-in-bootstrap-5/65254055#65254055)), `ml-auto` has been replaced with `ms-auto` to represent `start` instead of `left`. Since the Navbar is still based on , [auto margins OR flexbox utility classes](https://getbootstrap.com/docs/5.0/components/navbar/#how-it-works) are still used to align Navbar content.
For example, use `me-auto`...
```
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
        <a class="navbar-brand" href="#">Brand</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarContent">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                <li class="nav-item">
                    <a class="nav-link active" href="#">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Link</a>
                </li>
            </ul>
            <ul class="navbar-nav">
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false"> Menu </a>
                    <ul class="dropdown-menu dropdown-menu-end" aria-labelledby="navbarDropdown">
                        <li><a class="dropdown-item" href="#">Action</a></li>
                        <li><a class="dropdown-item" href="#">Another action</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
```

[Bootstrap 5 right align Navbar content](https://www.codeply.com/p/LJy7ooFrBP)

---


 (original answer)
Bootstrap has [many different ways to align navbar items](https://stackoverflow.com/questions/19733447/bootstrap-navbar-with-left-center-or-right-aligned-items). `float-right` won't work because the navbar is now `flexbox`.
You can use `mr-auto` for auto right margin on the  (left) `navbar-nav`.
, `ml-auto` could be used on the  (right) `navbar-nav` , or if you just have a single `navbar-nav`.
```
<nav class="navbar navbar-expand-md navbar-light bg-light">
    <a class="navbar-brand" href="#">Navbar</a>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav">
        <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarNav">
        <ul class="navbar-nav mr-auto">
            <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="#">Features</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="#">Pricing</a>
            </li>
        </ul>
        <ul class="navbar-nav">
            <li class="nav-item">
                <a class="nav-link" href="#">Login</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="#">Register</a>
            </li>
        </ul>
    </div>
</nav>
```

[https://codeply.com/go/P0G393rzfm](https://codeply.com/go/P0G393rzfm)
There are also flexbox utils. For example use `justify-content-end` on the collapse menu:
```
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Brand</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse justify-content-end" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
          <a class="nav-link active" aria-current="page" href="#">Contact</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">Pricing</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#">Download</a>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

Or when you have 2 `navbar-nav`s, use `justify-content-between` in `navbar-collapse` would work to even the space between the navbar-navs:
```
<div class="navbar-collapse collapse justify-content-between">
     <ul class="navbar-nav mr-auto">
               ..
     </ul>
     <ul class="navbar-nav">
           ..
     </ul>
 </div>
```


---



As of Bootstrap 4 beta, `ml-auto` will still work to push items to the right. Just be aware the the `navbar-toggleable-` classes have changed to `navbar-expand-*`
Updated [navbar right for Bootstrap 4](http://www.codeply.com/go/P0G393rzfm)

---


Another frequent Bootstrap 4 Navbar right alignment scenario includes a button on the right that  nav so that it is always shown at all widths.
[Right align button that is always visible](https://www.codeply.com/go/ljI9F6aRLk)
[](https://i.stack.imgur.com/IGYng.png)
[](https://i.stack.imgur.com/gMlCH.png)
Related: [Bootstrap NavBar with left, center or right aligned items](https://stackoverflow.com/questions/19733447/bootstrap-navbar-with-left-center-or-right-aligned-items/20362024#20362024)


------------
    
    
## Answer \#1

 Vote: 173

Created at 2017-01-08 01:49:36

------------

In my case, I wanted just one set of navigation buttons / options and found that this will work:

```
<div class="collapse navbar-collapse justify-content-end" id="navbarCollapse">
  <ul class="navbar-nav">
    <li class="nav-item">
      <a class="nav-link" href="#">Sign Out</a>
    </li>
  </ul>
</div>
```


So, you will add `justify-content-end` to the div and omit `mr-auto` on the list.

Here is a [working example](http://codepen.io/craigvantonder/pen/MJwLWb).


------------
    
    
## Answer \#2

 Vote: 78

Created at 2018-01-20 11:29:31

------------

For those who is still struggling with this issue in BS4 simply try below code - 

```
<ul class="navbar-nav ml-auto">
```



------------
    
    
## Answer \#3

 Vote: 52

Created at 2018-04-03 02:15:43

------------



If you want to align brand to your left and all the navbar-items to right, change the default `mr-auto` to `ml-auto`

```
<ul class="navbar-nav ml-auto">
```



------------
    
    
## Answer \#4

 Vote: 36

Created at 2017-12-11 23:16:47

------------

On `Bootsrap 4.0.0-beta.2`, none of the answers listed here worked for me. Finally, the Bootstrap site gave me the solution, not via its doc but via its page source code...

Getbootstrap.com align their right `navbar-nav` to the right with the help of the following class: `ml-md-auto`.


------------
    
    
## Answer \#5

 Vote: 19

Created at 2018-03-02 04:33:41

------------

Use this code to move items to the right.

```
<div class="collapse navbar-collapse justify-content-end">
```



------------
    
    
## Answer \#6

 Vote: 14

Created at 2017-10-29 03:22:00

------------

Use `ml-auto` instead of `mr-auto` after applying `nav` justify-content-end to the `ul`


------------
    
    
## Answer \#7

 Vote: 12

Created at 2017-07-24 09:44:06

------------

Just add `mr-auto` class at `ul`:

```
<ul class="nav navbar-nav mr-auto">
```


If you have menu list in both side you can do something like this:

```
<ul class="navbar-nav mr-auto">
  <li class="nav-item active">
    <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">Link</a>
  </li>
  <li class="nav-item">
    <a class="nav-link disabled" href="#">Disabled</a>
  </li>
</ul>
<ul class="navbar-nav ml-auto">
  <li class="nav-item active">
    <a class="nav-link" href="#">left 1</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">left 2</a>
  </li>
  <li class="nav-item">
    <a class="nav-link disabled" href="#">left disable</a>
  </li>
</ul>
```



------------
    
    
## Answer \#8

 Vote: 8

Created at 2017-06-08 22:12:57

------------

If you want Home, Features and Pricing on left immediately after your `nav-brand`, and then login and register on right then wrap the two lists in `<div>` and use `.justify-content-between`:

```
<div class="collapse navbar-collapse justify-content-between">
<ul>....</ul>
<ul>...</ul>
</div>
```



------------
    
    
## Answer \#9

 Vote: 8

Created at 2020-03-04 13:40:42

------------

In bootstrap v4.3 just add `ml-auto` in `<ul class="navbar-nav">`
Ex:`<ul class="navbar-nav ml-auto">`


------------
    
    
## Answer \#10

 Vote: 5

Created at 2017-10-09 19:31:44

------------

use the flex-row-reverse class

```
<nav class="navbar navbar-toggleable-md navbar-light">
    <div class="container">
        <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false"
          aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <a class="navbar-brand" href="#">
            <i class="fa fa-hospital-o fa-2x" aria-hidden="true"></i>
        </a>
        <div class="collapse navbar-collapse flex-row-reverse" id="navbarNavAltMarkup">
            <ul class="navbar-nav">
                <li><a class="nav-item nav-link active" href="#" style="background-color:#666">Home <span class="sr-only">(current)</span></a</li>
                <li><a class="nav-item nav-link" href="#">Doctors</a></li>
                <li><a class="nav-item nav-link" href="#">Specialists</a></li>
                <li><a class="nav-item nav-link" href="#">About</a></li>
            </ul>
        </div>
    </div>
</nav>
```



------------
    
    
## Answer \#11

 Vote: 5

Created at 2019-10-20 15:33:49

------------

It's little change in boostrap 4.
To align navbar to right side, you've to make only two changes.
they are:


1. in navbar-nav class add w-100 as navbar-nav w-100 to make width as 100
2. in nav-item dropdown class add ml-auto as nav-item dropdown ml-auto to make margin left as auto.



If you didn't understand, please refer the image that i've attached to this.

[CodePen Link](https://codepen.io/actionanand/pen/wvvoxMj)

[](https://i.stack.imgur.com/EqBYX.png)



```
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNavDropdown">
    <ul class="navbar-nav w-100">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Features</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Pricing</a>
      </li>
      <li class="nav-item dropdown ml-auto">
        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
          Dropdown link
        </a>
        <div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
          <a class="dropdown-item" href="#">Action</a>
          <a class="dropdown-item" href="#">Another action</a>
          <a class="dropdown-item" href="#">Something else here</a>
        </div>
      </li>
    </ul>  
  </div>
</nav>
```



------------
    
    
## Answer \#12

 Vote: 4

Created at 2019-11-04 03:39:40

------------

For bootstrap 4.3.1, I was using nav-pills and nothing worked for me except this:

```
<ul class="nav nav-pills justify-content-end ml-auto">
    <li ....</li>
    </ul>
```



------------
    
    
## Answer \#13

 Vote: 3

Created at 2021-04-18 21:37:48

------------

In my case Bootstrap v5, I wanted just one set of navigation  options to the right side:
> just add "ms-auto" in ul.
And it helped me.
```
<div class="collapse navbar-collapse" id="navbarCollapse">
    <ul class="navbar-nav ms-auto mb-2 mb-md-0">
      <li class="nav-item">
        <a class="nav-link " aria-current="page" href="index.php">Home</a>
      </li>
      <li class="nav-item">
        <a class="nav-link " href="services.php" tabindex="-1" aria-disabled="true">Services</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="aboutus.php">About Us</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="Portfolio.php">Portfolio</a>
      </li>
    </ul>
    
  </div>
```



------------
    
    
## Answer \#14

 Vote: 2

Created at 2021-05-12 17:04:30

------------

The above answers surprisingly didn't work for me so here is my approach. To align the  and  navigation bar items to the right you can make use of either of these two ways:

1. pull-right: Modify the unordered list tag for login and register as <ul class="navbar-nav pull-right">
2. navbar-right: Modify the unordered list tag for login and register as <ul class="navbar-nav navbar-right">




------------
    
    
## Answer \#15

 Vote: 0

Created at 2017-08-11 00:50:20

------------

I am running Angular 4 (v.4.0.0) and ng-bootstrap (Bootstrap 4). This code won't all be relevant but hoping people can pick and choose what works. It took me sometime to find a solution to get my items to justify right, collapse properly and to implement a dropdown off my google (using OAuth) profile picture. 

```
<div id="header" class="header">
  <nav class="navbar navbar-toggleable-sm navbar-inverse bg-faded fixed-top">
    <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarSupportedContent"
      aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
    <a class="navbar-brand" href="#">
          <img alt='Brand' src='../assets/images/logo-white.png' class='navbar-logo-img d-inline-block align-top '>
          <span class="navbar-logo-text">Oncoscape</span>
        </a>
    <div class="collapse navbar-collapse justify-content-end" id="navbarSupportedContent">
      <ul class="navbar-nav float-left">
        <a class="navbar-items nav-item nav-link active " *ngIf='authenticated' (click)='goDashboard()'>
          <span class="fa fa-dashboard"></span>Dashboard
        </a>
        <a class="nav-item nav-link navbar-items active" href="http://resources.sttrcancer.org/oncoscape-contact">
          <span class="fa fa-comments"></span>Feedback
        </a>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" id="navbarDropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            <img *ngIf='user && authenticated'  class="navbar-pic" src={{user.thumbnail}} alt="Smiley face">
          </a>
          <div *ngIf='user && authenticated' class="dropdown-menu " aria-labelledby="navbarDropdownMenuLink">
            <a class="dropdown-item" (click)="toProfile()">Account</a>
            <div class="dropdown-item">
              <app-login></app-login>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </nav>
</div>
<router-outlet></router-outlet>
```



------------
    
    
## Answer \#16

 Vote: 0

Created at 2017-08-19 21:31:12

------------

For Bootstrap 4 beta, sample navbar with elements aligned to the right side is:

```
<div id="app" class="container">
  <nav class="navbar navbar-expand-md navbar-light bg-faded">
    <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <a class="navbar-brand" href="#">Navbar</a>
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Features</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Pricingg</a>
      </li>
    </ul>
    <ul class="navbar-nav">
      <li class="nav-item">
        <a class="nav-link" href="{{ url('/login') }}">Login</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="{{ url('/register') }}">Register</a>
      </li>
    </ul>
  </nav>
</div>
```



------------
    
    
## Answer \#17

 Vote: 0

Created at 2017-10-23 09:06:48

------------

Using the bootstrap flex box helps 
us to control the placement and alignment of your navigation element.
for the problem above adding mr-auto is a better solution to it .

```
<div id="app" class="container">
  <nav class="navbar navbar-toggleable-md navbar-light bg-faded">
    <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <a class="navbar-brand" href="#">Navbar</a>
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Features</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Pricingg</a>
      </li>
    </ul>
    <ul class="navbar-nav  " >
      <li  class="nav-item">
        <a class="nav-link" href="{{ url('/login') }}">Login</a>
      </li>
      <li  class="nav-item">
        <a class="nav-link" href="{{ url('/register') }}">Register</a>
      </li>
    </ul>
  </nav>
  @yield('content')
</div>
```


other placement may include 

```
fixed- top
    fixed bottom
    sticky-top
```



------------
    
    
## Answer \#18

 Vote: 0

Created at 2017-11-14 09:23:28

------------

The working example for BS `v4.0.0-beta.2`:

```
<body>
    <nav class="navbar navbar-expand-md navbar-dark bg-dark">
      <a class="navbar-brand" href="#">Navbar</a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarNavDropdown">
        <ul class="navbar-nav mr-auto">
          <li class="nav-item active">
            <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Features</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Pricingg</a>
          </li>
        </ul>
        <ul class="navbar-nav">
          <li class="nav-item">
            <a class="nav-link" href="#">Login</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Register</a>
          </li>
        </ul>
      </div>
    </nav>


    <div class="container-fluid">
      container content
    </div>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="node_modules/jquery/dist/jquery.slim.min.js"></script>
    <script src="node_modules/popper.js/dist/umd/popper.min.js"></script>
    <script src="node_modules/bootstrap/dist/js/bootstrap.min.js"></script>
  </body>
```



------------
    
    
## Answer \#19

 Vote: 0

Created at 2018-05-19 20:52:39

------------

If all above fails, I added 100% width to the navbar class in CSS.  Until then mr auto wasn't working for me on this project using 4.1.


------------
    
    
## Answer \#20

 Vote: 0

Created at 2023-02-16 19:53:58

------------

- but the nav item you want to align to right in  alone- put it outside the  of main nav but in the same , like that:```
<ul class="navbar-nav justify-content-end ms-auto">
   <li class="nav-item">
     <a class="nav-link" href="#" aria-expanded="false">Contact support</a>
   </li>

   <li class="nav-item ">
     <a class="nav-link" href="#" aria-expanded="false">Log in</a>
   </li>
</ul>
```

use `justify-content-end ms-auto`


------------
    
    
## Answer \#21

 Vote: -1

Created at 2017-09-21 08:51:31

------------

Find the 69 line in the verndor-prefixes.less and write it following:

```
.panel {
    margin-bottom: 20px;
    height: 100px;
    background-color: #fff;
    border: 1px solid transparent;
    border-radius: 4px;
    -webkit-box-shadow: 0 1px 1px rgba(0,0,0,.05);
    box-shadow: 0 1px 1px rgba(0,0,0,.05);
}
```




------------
    
    
## Answer \#22

 Vote: -2

Created at 2018-02-12 11:14:13

------------

Just copied this from one of the getbootstrap pages for the released version 4 which worked much better than the above

```
<div class="d-none d-xl-block col-xl-2 bd-toc float-md-right">
    <ul class="section-nav">
         <li class="toc-entry toc-h2"><a href="#overview">Overview</a></li>
         <li class="toc-entry toc-h2"><a href="#classes">Classes</a></li>
         <li class="toc-entry toc-h2"><a href="#mixins">Mixins</a></li>
         <li class="toc-entry toc-h2"><a href="#responsive">Responsive</a></li>
    </ul>
</div>
```



------------
    
    
## Answer \#23

 Vote: -3

Created at 2017-04-03 11:57:44

------------

I'm new to stack overflow and new to front end development. This is what worked for me. So I did not want list items to be displayed.

```
.hidden {
  display:none;
  } 
  
 #loginButton{
 
 margin-right:2px;
 
 }
```
```
<nav class="navbar navbar-toggleable-md navbar-light bg-faded fixed-top">
  <button class="navbar-toggler navbar-toggler-right" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <a class="navbar-brand" href="#">NavBar</a>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active hidden">
        <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item hidden">
        <a class="nav-link" href="#">Link</a>
      </li>
      <li class="nav-item hidden">
        <a class="nav-link disabled" href="#">Disabled</a>
      </li>
    </ul>
    <form class="form-inline my-2 my-lg-0">
      <button class="btn btn-outline-success my-2 my-sm-0" type="submit" id="loginButton"><a href="#">Log In</a></button>
      <button class="btn btn-outline-success my-2 my-sm-0" type="submit"><a href="#">Register</a></button>
    </form>
  </div>
</nav>
```




------------
    
    