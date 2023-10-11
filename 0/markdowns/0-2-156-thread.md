
# Post \#60585366 [Link](https://stackoverflow.com/questions/60585366/)

## Change button position in HTML & CSS?

**Vote**: 2 (631/702) **Views**: 17366 (328/702) 

**Internal ID** \#0-2-156

Created at 2020-03-08 07:26:44

Tags: `html` `css`

----------

#### Metadata:

Area: `Front-end`

Language: `html` (full parsed tag list: `html`)

----------

**Notepad**


----------

I wrote a CSS file for my HTML Page but it make my page mess up. The stop button is behind the game frame.

1) How should I move stop button at Top to properly align with begin button.

2) The bottom arrows going out of screen how should I slightly move it up so they would display properly?

3) How To add color on the text Points Earned and click to start the game?

![The image is avilable here](https://i.stack.imgur.com/HY836.png)

HTML code:

```
<html>
<head>
    <link rel="icon" href="./arrows/clubbackground.jpg" type="image/gif" sizes="16x16">
    <script src="https://code.jquery.com/jquery-1.11.2.min.js"></script>
    <script src="jsRev.js" type="text/javascript"></script>
    <link rel="stylesheet" type="text/css" href="style.css">
    <title>DDR-Project 1</title>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<div id="BackgroundScene">
  <div id="DanceScoreboard">
    <div id="GameStopped"><button id="StartBtn" class="btnStyle" onclick="gameStarted=true;">Begin Game</button>
      <br><br><br>
      <div class="Status">Click Begin Game to start</div>
    </div>
    <div id="GameRunning"><button id="StopBtn" class="btnStyle" onclick="self.close()">Stop Game</button>
      <div id="Status" class="Status"></div>
    </div>
    <div id="dancePoints" class="Points">Points Earned:
      <div class="OutputText" id="CorrectCount">0</div>
    </div>
  </div>
  <div class="stage"></div>
  <!-- ENDS .STAGE -->
  <div id="controls">
    <img id="left" src="./arrows/staticLeft.png">
    <img id="up" src="./arrows/staticUp.png">
    <img id="down" src="./arrows/staticDown.png">
    <img id="right" src="./arrows/staticRight.png">
  </div>
  <!-- ENDS #CONTROLS -->

</body>
</html>
```


CSS file:

```
.stage {
    width: 1150px;    
    height: 500px;
    margin:0;
    padding:0;
    background-image: url('./arrows/clubbackground.jpg');
    background-repeat: no-repeat;
    background-size: contain;
    position: relative;
    text-align: center;
    opacity: 90%;

}

#controls {
    padding-left: 350px;

}
/*#left {

}
#up {

}
#down {

}
#right {

}*/
.btnStyle {
    border-radius: 4px;
    border: medium solid #000000;
    background-color:rgb(92, 250, 246);
    font-family: "Arial", Impact, Charcoal, sans-serif;
    font-weight: bold;
    font-size:20px;
    color: #ffffff;
    padding: 5px 30px;
    margin:8px 70px;
}

#StartBtn{
    position: fixed;
    left: 100px;
    text-shadow: 2px 2px 4px black;
}

#StopBtn{
    position: fixed;
    left: 500px;
    down: 10px;
    border-radius: 4px;
    border: medium solid #000000;
    background-color:rgb(92, 250, 246);
    font-family: "Arial", Impact, Charcoal, sans-serif;
    font-weight: bold;
    font-size:20px;
    color: #ffffff;
    text-shadow: 2px 2px 4px black;
    padding: 5px 30px;
    margin:8px 70px;
}
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-03-08 07:42:53

------------

You can use tables to control positioning in your HTML projects. It's probably not the most efficient way, but it's beginner friendly.

```
<html>
<head>
    <link rel="icon" href="./arrows/clubbackground.jpg" type="image/gif" sizes="16x16">
    <script src="https://code.jquery.com/jquery-1.11.2.min.js"></script>
    <script src="jsRev.js" type="text/javascript"></script>
    <link rel="stylesheet" type="text/css" href="style.css">
    <title>DDR-Project 1</title>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<div id="BackgroundScene">
  <div id="DanceScoreboard">
    **<table>
       <tr>
          <td>
              <div id="GameStopped"><button id="StartBtn" class="btnStyle" 
                 onclick="gameStarted=true;">Begin Game</button>
          </td>
           <td>
                <div id="GameRunning"><button id="StopBtn" class="btnStyle" 
                 onclick="self.close()">Stop Game</button>
           </td>
      </tr>
   </table>**   
<br><br><br>
          <div class="Status">Click Begin Game to start</div>
        </div>
        <div id="GameRunning"><button id="StopBtn" class="btnStyle" onclick="self.close()">Stop Game</button>
          <div id="Status" class="Status"></div>
        </div>
        <div id="dancePoints" class="Points">Points Earned:
          <div class="OutputText" id="CorrectCount">0</div>
        </div>
      </div>
      <div class="stage"></div>
      <!-- ENDS .STAGE -->
      <div id="controls">
        <img id="left" src="./arrows/staticLeft.png">
        <img id="up" src="./arrows/staticUp.png">
        <img id="down" src="./arrows/staticDown.png">
        <img id="right" src="./arrows/staticRight.png">
      </div>
      <!-- ENDS #CONTROLS -->

    </body>
    </html>
```


Since I don't have access to a working environment at the moment, I can't test it, but it should work ok. 
I recommend that you would also read about all the values the  tag can accept, because there are some great ones for display purposes. 

BTW a general tip: always specify width and height using percent and not pixels, as pixels differ from screen to screen.

Hope I have been helpful :).       


------------
    
    
## Answer \#1

 Vote: 1

Created at 2020-03-08 07:38:07

------------

You are using a fixed position for your #StopBtn.
Simply add `top:10px;` to your #StopBtn ID in css file. 

See  jsfiddle:
[https://jsfiddle.net/2sgte9up/](https://jsfiddle.net/2sgte9up/)

*A fixed position element is positioned relative to the viewport, or the browser window itself. The viewport doesn't change when the window is scrolled, so a fixed positioned element will stay right where it is when the page is scrolled. You can call on the top, right, left, bottom properties.  

For color to your points earned, add css color to css file for your CorrectCount ID:

```
#CorrectCount{
  color: #;
}
```


For the buttons on the bottom, consider adding fixed position for them as well and set your bottom or create a footer div and place them in your footer div.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2020-03-08 07:43:25

------------

Use some Flex Properties to fix it. Try this

```
.stage {
    width: 1150px;    
    height: 500px;
    margin:0;
    padding:0;
    background-image: url('./arrows/clubbackground.jpg');
    background-repeat: no-repeat;
    background-size: contain;
    position: relative;
    text-align: center;
    opacity: 90%;

}

#controls {
    padding-left: 350px;

}
/*#left {

}
#up {

}
#down {

}
#right {

}*/

.Points{
color:blue;
}

.btnStyle {
    border-radius: 4px;
    border: medium solid #000000;
    background-color:rgb(92, 250, 246);
    font-family: "Arial", Impact, Charcoal, sans-serif;
    font-weight: bold;
    font-size:20px;
    color: #ffffff;
    padding: 5px 30px;
    margin:8px 70px;
}

#DanceScoreboard{
display: flex;
}

#StartBtn{
  flex: 1;}

#StopBtn{
  flex: 1;}
```
```
<html>
<head>
    <link rel="icon" href="./arrows/clubbackground.jpg" type="image/gif" sizes="16x16">
    <script src="https://code.jquery.com/jquery-1.11.2.min.js"></script>
    <script src="jsRev.js" type="text/javascript"></script>
    <link rel="stylesheet" type="text/css" href="style.css">
    <title>DDR-Project 1</title>
</head>
<body>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<div id="BackgroundScene">
  <div id="DanceScoreboard">
    <div id="GameStopped"><button id="StartBtn" class="btnStyle" onclick="gameStarted=true;">Begin Game</button>
      <br><br><br>
      <div class="Status">Click Begin Game to start</div>
    </div>
    <div id="GameRunning"><button id="StopBtn" class="btnStyle" onclick="self.close()">Stop Game</button>
      <div id="Status" class="Status"></div>
    </div>
  </div>
<div id="dancePoints" class="Points">Points Earned:
      <div class="OutputText" id="CorrectCount">0</div>
    </div>
  <div class="stage"></div>
  <!-- ENDS .STAGE -->
  <div id="controls">
    <img id="left" src="./arrows/staticLeft.png">
    <img id="up" src="./arrows/staticUp.png">
    <img id="down" src="./arrows/staticDown.png">
    <img id="right" src="./arrows/staticRight.png">
  </div>
  <!-- ENDS #CONTROLS -->

</body>
</html>
```




------------
    
    
## Answer \#3

 Vote: 1

Created at 2020-03-08 07:44:59

------------

I am not big fan of `position:fixed` you may have to use something like this.

```
.stage {
    width: 1150px;    
    height: 500px;
    margin:0;
    padding:0;
    background-image: url('./arrows/clubbackground.jpg');
    background-repeat: no-repeat;
    background-size: contain;
    position: relative;
    text-align: center;
    opacity: 90%;

}

#controls {
    padding-left: 350px;

}

.btnStyle {
    border-radius: 4px;
    border: medium solid #000000;
    background-color:rgb(92, 250, 246);
    font-family: "Arial", Impact, Charcoal, sans-serif;
    font-weight: bold;
    font-size:20px;
    color: #ffffff;
    padding: 5px 30px;
    margin:8px 70px;
}

#StartBtn{
    position: absolute;
    left: 100px;
    text-shadow: 2px 2px 4px black;
}

#StopBtn{
    position: absolute;
    left: 400px;
    top:5px;
    border-radius: 4px;
    border: medium solid #000000;
    background-color:rgb(92, 250, 246);
    font-family: "Arial", Impact, Charcoal, sans-serif;
    font-weight: bold;
    font-size:20px;
    color: #ffffff;
    text-shadow: 2px 2px 4px black;

}

.OutputText{
color: red;
}
```
```
<div id="BackgroundScene">
  <div id="DanceScoreboard">
    <div id="GameStopped"><button id="StartBtn" class="btnStyle" onclick="gameStarted=true;">Begin Game</button>
      <br><br><br>
      <div class="Status">Click Begin Game to start</div>
    </div>
    <div id="GameRunning"><button id="StopBtn" class="btnStyle" onclick="self.close()">Stop Game</button>
      <div id="Status" class="Status"></div>
    </div>
    <div id="dancePoints" class="Points">Points Earned:
      <div class="OutputText" id="CorrectCount">0</div>
    </div>
  </div>
  <div class="stage"></div>
  <!-- ENDS .STAGE -->
  <div id="controls">
    <img id="left" src="./arrows/staticLeft.png">
    <img id="up" src="./arrows/staticUp.png">
    <img id="down" src="./arrows/staticDown.png">
    <img id="right" src="./arrows/staticRight.png">
  </div>
  <!-- ENDS #CONTROLS -->
```




------------
    
    