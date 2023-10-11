
# Post \#70106748 [Link](https://stackoverflow.com/questions/70106748/)

## How To Insert Image Inside of Div Element HTML

**Vote**: 1 (672/702) **Views**: 2989 (570/702) 

**Internal ID** \#0-2-159

Created at 2021-11-25 06:35:03

Tags: `html` `css`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I am wondering how to insert an image inside of a div element in HTML. I have tried inserting an image through  and through  inside of a div element, but none of those have worked for me and I am not sure how to get it to work. I am wondering how to get my image inserted inside of the div element, so my program will actually display it. The problem is that my image never displays and my solutions did not work for me. Here is the simple and full code: [https://jsfiddle.net/mxafg1vu/](https://jsfiddle.net/mxafg1vu/)
```
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">

</head>
<body>

<div class="header">
<div id ="header">
<h2 style="text-indent: 1em; font-family: Helvetica; color: blue;">Holograms</h2>
</div></div><br>


<div class="row">
  <div class="leftcolumn">
   <div class="card">
    <div id="title">
      <h2>Holograms In The Future?</h2>
      <h5>November 24th, 2021.</h5>

      <!-- <div class="hologram.jpg" style="height:200px; width: 200px"></div> -->
       <!--Img src -->
      <img src="hologram.jpg" alt="Holograms" width="500" height="500"><br>

      <p>Text </p>
      <p>Text </p>
    </div>
    </div> 
    </div>
    </div>

<div class="footer">
  <div id="footer">
 </div>

</body>
</html>
```



----------
        
## Answer \#0

**Accepted** Vote: 2

Created at 2021-11-25 06:45:07

------------

It works! I think that your path is just wrong. In your example the `alt` tag gets displayed, the picture is trying to load, but is not finding the image.
If the image is in the same directory as the .html:
`src="image.png"`
If it is in a directory below your .html: `src="../asdf/image.png"`
And if it isn't localy stored, they just insert the url, like in my example below:
```
* {
  box-sizing: border-box;
}

/* Add a gray background color with some padding */
body {
  font-family: Arial;
  padding: 20px;
  background: #32cd32;
}

/* Header/Blog Title */
.header {
  padding: 30px;
  font-size: 40px;
  text-align: center;
  background: #7df9ff;
}

/* Create two unequal columns that floats next to each other */
/* Left column */
.leftcolumn {   
  float: left;
  width: 100%;
}

/* Fake image */
.fakeimg {
  background-color: #aaa;
  width: 100%;
  padding: 20px;
}

/* Add a card effect for articles */
.card {
   background-color: #87ceeb;
   padding: 20px;
   margin-top: 20px;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}

/* Footer */
.footer {
  padding: 20px;
  text-align: center;
  background: #00bfff;
  margin-top: 20px;
}

.fa {
  padding: 20px;
  font-size: 30px;
  width: 50px;
  text-align: center;
  text-decoration: none;
  margin: 5px 2px;
}

.fa:hover {
    opacity: 0.7;
}

.fa-google {
  background: #dd4b39;
  color: white;
}


.fa-youtube {
  background: #bb0000;
  color: white;
}

.fa-facebook {
  background: #3B5998;
  color: white;
}

.fa-twitter {
  background: #55ACEE;
  color: white;
}

#hologram {
   width: 48px;
   height: 48px;
}

#container img {
   width: 100%;
}


/* Responsive layout - when the screen is less than 800px wide, make the two columns stack on top of each other instead of next to each other */
@media screen and (max-width: 800px) {
  .leftcolumn, .rightcolumn {   
    width: 100%;
    padding: 0;
  }
}
```
```
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">

</head>
<body>

<div class="header">
<div id ="header">
<h2 style="text-indent: 1em; font-family: Helvetica; color: blue;">Holograms</h2>
</div></div><br>


<div class="row">
  <div class="leftcolumn">
   <div class="card">
    <div id="title">
      <h2>Holograms In The Future?</h2>
      <h5>November 24th, 2021.</h5>

      <!-- <div class="hologram.jpg" style="height:200px; width: 200px"></div> -->
       <!--Img src -->
      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/Stack_Overflow_logo.svg/800px-Stack_Overflow_logo.svg.png" alt="Holograms" width="500"><br>

      <p>Text </p>
      <p>Text </p>
    </div>
    </div> 
    </div>
    </div>

<div class="footer">
<div id="footer">
 </div>

</body>
</html>
```




------------
    
    
## Answer \#1

 Vote: 1

Created at 2021-11-25 06:43:21

------------

To display images you have to specifies the correct path to the image on  attribute;
```
<img src="https://raw.githubusercontent.com/sayeedap/sayeedap/main/images/stackoverflow.svg" alt="Girl in a jacket" width="500" height="600">
```

[https://jsfiddle.net/Lha89uqv/](https://jsfiddle.net/Lha89uqv/)


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-11-25 06:43:31

------------

The `src` or `href` attribute requires an attribute to link any external source to HTML file.
Following are the different types to specify file paths:
```
<img src="picture.jpg"> It specifies that picture.jpg is located in the same folder as the current page.

<img src="images/picture.jpg"> It specifies that picture.jpg is located in the images folder in the current folder.

<img src="/images/picture.jpg"> It specifies that picture.jpg is located in the images folder at the root of the current web.

<img src="../picture.jpg"> It specifies that picture.jpg is located in the folder one level up from the current folder.
```

Example
```
<!DOCTYPE html>  
<html>  
<body>  
  <h2>Using a Relative File Path</h2>  
  <img src="/images/nature-2.jpg" alt="Mountain" style="width:300px">  
</body>  
</html>
```



------------
    
    
## Answer \#3

 Vote: 1

Created at 2021-11-25 06:47:11

------------

```
<div id="title">
  <h2>Holograms In The Future?</h2>
  <h5>November 24th, 2021.</h5>
  <div id ="hologram">      
     <!--Img src -->
     <img src="hologram.jpg" alt="Holograms"><br>
  </div><br>
  <p>Text </p>
  <p>Text </p>
</div>
```

Make sure the image path is correct.
```
$("#hologram").prepend(`<img src="https://i.picsum.photos/id/938/200/200.jpg?hmac=92xbyQ6vbS3815L2g4uCHhtDVrdsdJfHq76-23joI3M" alt="Holograms">`);
```

[Correct Image Url](https://i.stack.imgur.com/Q9E4i.png)


------------
    
    